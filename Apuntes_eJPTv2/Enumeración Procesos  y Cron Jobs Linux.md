# ⚙️Procesos y Tareas Programadas (Cron) en Linux
El objetivo es identificar qué programas se están ejecutando y qué tareas se disparan automáticamente. Buscamos procesos con privilegios elevados o scripts de limpieza/respaldo que podamos manipular.
## 🎯 1. ¿Qué buscamos?

- **Servicios y Procesos:** Identificar qué aplicaciones están activas. Nos interesa ver si hay procesos corriendo como `root` que no deberían o versiones de software vulnerables.
- **Cron Jobs (Tareas Programadas):** En Linux, `cron` es el equivalente a las tareas programadas de Windows. Si una tarea corre como `root` y ejecuta un script al que tenemos acceso de escritura, **podemos escalar privilegios**.
- **Permisos de Escritura:** Archivos de configuración o binarios en ejecución que podamos modificar.
## 💻 2. Comandos de Enumeración (Nativos)

### 📊 Procesos del Sistema

|**Comando**|**Función**|
|---|---|
|`ps`|Muestra los procesos de la sesión actual.|
|`ps aux`|**El estándar:** Muestra todos los procesos de todos los usuarios con CPU, RAM y usuario dueño.|
|`top`|Monitor dinámico de procesos en tiempo real.|
|`ps -ef`|Otra forma de ver todos los procesos; ayuda a ver la jerarquía (PPID).|

### ⏰ Cron Jobs (Tareas Programadas)

|**Comando**|**Función**|
|---|---|
|`crontab -l`|Muestra las tareas programadas del usuario actual.|
|`ls -al /etc/cron*`|Lista todos los directorios de cron (`cron.daily`, `cron.hourly`, `cron.d`, etc.).|
|`cat /etc/crontab`|Muestra el archivo principal de cron del sistema.|
|`cat /etc/cron.d/*`|Revisa configuraciones de tareas programadas de aplicaciones específicas.|
## ⚡ 3. Enumeración vía Meterpreter

Meterpreter facilita la gestión de procesos y la ocultación de nuestra presencia:

- **`ps`**: Lista todos los procesos, su arquitectura y el usuario que los corre.
- **`pgrep <nombre>`**: Encuentra rápidamente el ID de un proceso por su nombre (ej: `pgrep mysql`).
- **`migrate <PID>`**: Mueve tu sesión a otro proceso más estable o con más privilegios (si tienes permisos).
- **`getpid`**: Te indica en qué proceso estás operando actualmente.

## 💡 4. Tips para el eJPTv2 (Vectores de Escalada)

1. **Scripts en Crontab:** Si ves una línea en `/etc/crontab` como: `* * * * * root /home/user/backup.sh` Y tú tienes permisos de escritura en `backup.sh`, puedes añadir una reverse shell al final del archivo y recibirás una conexión como `root` en un minuto.
2. **Procesos de Root:** En `ps aux`, busca procesos que corran como `root` pero que no sean servicios estándar del sistema. Podrían ser aplicaciones vulnerables a un _Buffer Overflow_ o con fallos de configuración.
3. **Rutas Relativas en Cron:** Si una tarea cron ejecuta un comando sin la ruta completa (ej: `tar` en lugar de `/bin/tar`), podrías intentar un ataque de **PATH Hijacking**.


---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]
- [[Cron Jobs Mal Configurados]]