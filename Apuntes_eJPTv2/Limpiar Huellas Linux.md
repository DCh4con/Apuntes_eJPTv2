# 🧹 Borrado de Huellas en Linux 
El objetivo es eliminar el rastro de los comandos ejecutados y los archivos manipulados. En Linux, el principal "delator" es el archivo `.bash_history`, que almacena cronológicamente todo lo que escribimos en la terminal.

## 🕒 1. Gestión del Historial (.bash_history)
Existen varias formas de manejar el historial, desde el borrado total hasta la edición selectiva.
### A. Borrado Total (Ruidoso)
Este comando limpia el historial de la sesión actual, pero es muy sospechoso porque deja el archivo vacío.
- `history -c && history -w`
	- `-c`: Limpia el historial en memoria.
	- `-w`: Escribe los cambios en el archivo `.bash_history` (dejándolo vacío).
- `cat /dev/null > .bash_history`: Borra los comandos en disco

### B. Borrado Selectivo (Sigiloso) 🎯
Es la técnica más recomendada. En lugar de borrar todo, eliminamos solo las líneas que contienen nuestros comandos (como `wget`, `nmap` o la ejecución de exploits).
**Pasos:**

1. Ver el historial con números de línea: `history`
2. Borrar una línea específica por su número:
	- `history -d <número_de_línea>`

3. **Truco:** Para borrar un rango o comandos específicos sin dejar rastro, puedes editar el archivo directamente con `nano` o `vi`:
	- `nano ~/.bash_history`
	- Borra las líneas que quieras y guarda el archivo.

### C. Evitar que se guarde el historial (Preventivo)
Si no quieres tener que borrar nada al final, puedes indicarle a la shell que no guarde lo que vas a escribir en esa sesión:
- `unset HISTFILE`
- Después de ejecutar esto, ningún comando de esa sesión se guardará en el disco.

## 📂 2. Limpieza de Archivos Temporales
Como buena práctica aprendida en Windows, en Linux también debemos ser ordenados:

1. **Directorio de trabajo:** Usa siempre `/tmp` o `/dev/shm`.
    - `/dev/shm` es un sistema de archivos en RAM; al reiniciar la máquina, todo lo que haya allí desaparece.
2. **Borrado de herramientas:**
	- Ej: `rm /tmp/LinEnum.sh /tmp/exploit.py /tmp/shell.php`

## 📑 3. Limpieza de Logs del Sistema (Opcional)
Aunque el eJPTv2 no profundiza aquí, es bueno saber que los logs de autenticación (quién se logueó y cuándo) se guardan en:

- `/var/log/auth.log` (Sistemas basados en Debian/Ubuntu)
- `/var/log/secure` (Sistemas basados en RedHat/CentOS)

Si tienes privilegios de **root**, podrías editar estos archivos para eliminar las líneas donde aparece tu IP de atacante.

## 💡 Resumen para el examen eJPTv2
Si el examen te pide limpiar tus huellas tras comprometer una máquina Linux:

1. Ve a tu directorio de trabajo y borra tus scripts (`rm`).
2. Ejecuta `history -c` para limpiar la sesión.
3. Si quieres ser más profesional, usa `history -d` para borrar solo el comando del exploit y la descarga del script.

---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]