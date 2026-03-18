# 👥 Usuarios y Grupos en Linux
El objetivo de esta fase es identificar con qué usuario hemos entrado, qué nivel de acceso tenemos y quiénes son los "vecinos" en el sistema que podrían tener privilegios más altos o archivos interesantes.

## 🎯 1. ¿Qué buscamos?

- **Identidad:** ¿Quién soy (`UID`) y en qué grupos estoy?
- **Privilegios:** ¿Tengo permisos de sudo o pertenezco a grupos críticos (como `docker`, `lxd`, `shadow` o `disk`)?
- **Usuarios Reales:** Diferenciar entre usuarios del sistema (servicios) y usuarios humanos que pueden tener carpetas personales en `/home/`.
- **Actividad:** ¿Quién ha entrado recientemente? Esto ayuda a identificar cuentas activas.

## 💻 2. Comandos de Enumeración (Nativos)

### 👤 Usuario Actual
| **Comando**        | **Función**                                                          |
| ------------------ | -------------------------------------------------------------------- |
| `whoami`           | Muestra el nombre del usuario actual.                                |
| `id`               | Muestra el **UID**, **GID** y todos los grupos a los que perteneces. |
| `groups`           | Lista los grupos del sistema o de tu usuario.                        |
| `groups <usuario>` | Muestra específicamente a qué grupos pertenece otro usuario.         |
### 📂 Usuarios y Grupos del Sistema
| **Comando**                            | **Función**                                                                                      |
| -------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `cat /etc/passwd`                      | Lista todos los usuarios. El formato es `usuario:x:UID:GID:...`                                  |
| `cat /etc/passwd \| grep -v "nologin"` | **Filtro útil:** Excluye cuentas de servicio para ver solo usuarios con shell interactiva (SSH). |
| `cat /etc/group`                       | Muestra todos los grupos creados en el sistema.                                                  |
| `lastlog`                              | Muestra la última vez que cada usuario inició sesión.                                            |
## ⚡ 3. Enumeración vía Meterpreter
Si estás usando una sesión de Meterpreter en Linux:

- **`getuid`**: Te da el nombre y el ID del usuario actual.

## 💡 4. Tips para el eJPTv2

1. **El archivo /etc/passwd:** Siempre busca usuarios con **UID 1000** o superior; suelen ser los usuarios creados por humanos. El UID **0** es siempre `root`.
2. **Grupos Peligrosos:** * **sudo/wheel:** Pueden ejecutar comandos como root.
    - **adm:** Suelen tener acceso a archivos de logs en `/var/log`.
    - **docker/lxd:** Permiten escalar a root casi instantáneamente mediante contenedores.
3. **Shells:** Al mirar `/etc/passwd`, fíjate en el final de la línea. Si termina en `/bin/bash` o `/bin/sh`, es un usuario con el que podrías intentar una conexión SSH si consigues su contraseña.


---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]