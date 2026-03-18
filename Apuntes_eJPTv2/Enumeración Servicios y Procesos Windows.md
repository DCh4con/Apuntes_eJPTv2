# ⚙️ Post-Explotación: Servicios, Procesos y Tareas en Windows
El objetivo de esta fase es identificar qué software está operando en el sistema y cómo se mantiene en ejecución. Buscamos servicios con permisos débiles o tareas programadas que ejecuten archivos que podamos modificar.

## 🎯 1. ¿Qué buscamos?

- **Servicios:** Programas que corren en segundo plano, a menudo con privilegios elevados (`SYSTEM`). Queremos saber qué servicios están activos y bajo qué usuario corren.
- **Procesos:** Aplicaciones activas en memoria. Nos interesa identificar procesos de administración o de seguridad (Antivirus, EDR).
- **Tareas Programadas:** Acciones que Windows ejecuta automáticamente. Son vectores comunes de escalada si ejecutan scripts o binarios en rutas donde tenemos permisos de escritura.

## 💻 2. Comandos de Enumeración (CMD)
### 🛠️ Servicios

| **Comando**                   | **Función**                                                              |
| ----------------------------- | ------------------------------------------------------------------------ |
| **`net start`**               | Lista los servicios que están iniciados actualmente.                     |
| **`wmic service list brief`** | Muestra una lista resumida de todos los servicios (nombre, estado, PID). |
| **`sc query`**                | Comando estándar para consultar el estado de servicios específicos.      |

### 📑 Procesos y Tareas

| **Comando**                       | **Función**                                                                                |
| --------------------------------- | ------------------------------------------------------------------------------------------ |
| **`tasklist /SVC`**               | Muestra los procesos activos y, lo más importante, qué servicios dependen de cada proceso. |
| **`schtasks /query /fo LIST /v`** | Muestra una lista detallada (verbose) de todas las **Tareas Programadas**.                 |

>[!warning] **Análisis de Tareas Programadas** Al revisar `schtasks`, busca el campo **"Task to Run"**. Si ves que una tarea ejecuta un archivo en una carpeta como `C:\Temp\` o `C:\Users\Public\` (Cualquier carpeta donde tengas permisos de escritura), podrías intentar reemplazar ese archivo por un payload para ganar privilegios cuando la tarea se ejecute.

## ⚡ 3. Enumeración vía Meterpreter
Meterpreter ofrece una visión mucho más dinámica y fácil de filtrar de los procesos:

- **`ps`**: Muestra todos los procesos, su arquitectura (x86/x64), el usuario que los corre y su ruta en el disco.
- **`pgrep <nombre>`**: Busca el **PID** (Process ID) de un proceso específico por su nombre (ej: `pgrep explorer.exe`).
- **`getpid`**: Te dice el ID del proceso en el que estás "viviendo" actualmente.
- **`migrate <PID>`**: Te permite saltar a otro proceso. _Tip: Migra siempre a procesos estables como `explorer.exe` o `svchost.exe`._


---
**Navegación:**
- [[5.1 Post-Explotación Windows]]