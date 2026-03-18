# 🛡️ Escalada de Privilegios: SUDO
En Linux, el comando `sudo` (SuperUser DO) permite a los usuarios ejecutar programas con los privilegios de seguridad de otro usuario (normalmente el usuario `root`). Si estos permisos no están restringidos correctamente, el sistema es vulnerable.

## 🔍 1. Enumeración de Privilegios
Lo primero que debemos hacer al ganar acceso a una shell de Linux es preguntar: _"¿Qué puedo ejecutar como root?"_
- `sudo -l`
### ¿Qué buscamos en la salida?

- **User may run the following commands on this host:** Lista de binarios permitidos.
- **(root) NOPASSWD:** Esto es una mina de oro. Significa que puedes ejecutar ese binario como root **sin conocer la contraseña** del usuario actual.
- **ALL : ALL**: Si ves esto, el usuario tiene permisos totales de administrador.

## 🚀 2. Explotación con GTFOBins
Cuando identificas un binario que puedes ejecutar con `sudo`, el siguiente paso es consultar **GTFOBins**.

> [!important] **¿Qué es GTFOBins?** **[GTFOBins](https://gtfobins.github.io/)** es una lista curada de binarios de Unix que pueden ser utilizados para saltar restricciones de seguridad del sistema.

### Pasos para explotar:

1. Entra en [gtfobins.github.io](https://gtfobins.github.io/).
2. Busca el nombre del binario que viste en `sudo -l` (ejemplo: `vim`, `find`, `nano`, `awk`).
3. Haz clic en la función **Sudo**.
4. Copia y ejecuta el comando proporcionado.

## 💡 3. Ejemplos Comunes en el eJPTv2
Aquí tienes algunos ejemplos de cómo un binario inofensivo se convierte en una shell de root:
### Caso A: VIM
Si `sudo -l` dice que puedes usar `vim`, puedes spawnear una shell desde dentro del editor:
- `sudo vim -c ':!/bin/sh'`
### Caso B: FIND
Si puedes usar `find`, puedes ejecutar comandos con el flag `-exec`:
- `sudo find . -exec /bin/sh \; -quit`
### Caso C: AWK
Un lenguaje de procesamiento de texto que permite ejecutar llamadas al sistema:
- `sudo awk 'BEGIN {system("/bin/sh")}'`

## 🚩 Checklist de Seguridad

- **Verificar el PATH:** A veces el binario permitido no usa una ruta absoluta (ej. `python` en lugar de `/usr/bin/python`). Esto podría permitir un ataque de **PATH Hijacking**.


---
**Navegación:**
- Volver a: [[3.5 Escalada de Privilegios en Linux]]