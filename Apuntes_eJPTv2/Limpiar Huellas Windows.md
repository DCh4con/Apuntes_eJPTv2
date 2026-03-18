# 🧹 Borrado de Huellas en Windows
El objetivo es no dejar rastro de nuestra actividad en el sistema una vez finalizada la auditoría. Esto incluye eliminar archivos subidos, revertir cambios en el registro y limpiar los registros de eventos del sistema.

## 🚀 1. Buenas Prácticas de Trabajo
Para facilitar la limpieza, es vital ser organizado desde el primer minuto de la intrusión:

- **Directorio Centralizado:** Crea siempre una carpeta temporal en una ruta común para subir tus herramientas.
    - `mkdir C:\Temp`
    - **Ventaja:** Al terminar, solo necesitas borrar una carpeta para eliminar todos tus binarios (`rmdir /s /q C:\Temp`).
- **Anotación de Cambios:** Lleva un registro de cada archivo subido, cada usuario creado y cada servicio modificado. Si no sabes qué cambiaste, no podrás restaurarlo.

## ⚡ 2. Limpieza Automática en Metasploit
Cuando usas módulos de explotación que realizan cambios persistentes (como `exploit/windows/persistence/service`), Metasploit suele generar automáticamente un **script de limpieza** (Resource Script).
### Cómo usar los scripts `.rc`:
Al ejecutar el módulo de persistencia, Metasploit te mostrará en pantalla la ruta de un archivo con extensión `.rc`. 

![[Imagenes/MetasploitCleanResource.png]]

Para limpiar todo automáticamente, ejecuta en Meterpreter:
- `meterpreter > resource /ruta/del/script_proporcionado.rc`

>[!info] **¿Qué hace esto?** Este comando ejecuta una serie de instrucciones predefinidas que detienen el servicio malicioso, lo eliminan del registro y borran el ejecutable del disco (Para este ejemplo).

## 📑 3. Limpieza de Logs (Event Logs)
Windows registra casi todo (logins, errores, cambios de privilegios) en los "Event Logs". Meterpreter tiene un comando "mágico" para limpiar esto de un plumazo:
- `meterpreter > clearev`
	- **¿Qué hace?** Borra los registros de **Application**, **System** y **Security**.

> [!danger] **Nota:** En un entorno real, el hecho de que los logs desaparezcan repentinamente es una alerta roja para un equipo de respuesta a incidentes (Blue Team).


---
**Navegación:**
- Anterior: [[5.1 Post-Explotación Windows]]