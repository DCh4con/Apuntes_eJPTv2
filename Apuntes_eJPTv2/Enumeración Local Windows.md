El objetivo de esta fase es recolectar toda la información técnica posible del sistema comprometido. Un detalle pequeño (como un parche que falta o una arquitectura de 32 bits) puede ser la diferencia entre obtener privilegios de **SYSTEM** o bloquear la máquina.

## 🎯 1. ¿Qué información buscamos?

Para realizar una escalada de privilegios efectiva, necesitamos identificar:

- **Identidad:** Hostname (nombre del equipo) y usuario actual.
- **Sistema Operativo:** Versión concreta (Windows 7, 8.1, 10, Server 2016, etc.).
- **Precisión Técnica:** Build del SO y Service Pack (ej: Windows 7 SP1 Build 7600).
- **Arquitectura:** ¿Es un sistema de 32 bits (x86) o de 64 bits (x64)?
- **Defensas:** Parches de seguridad (Hotfixes) instalados para saber qué vulnerabilidades han sido corregidas.

## 💻 2. Enumeración vía Comandos de Windows (CMD)
Si tienes una shell convencional o has usado el comando `shell` dentro de Meterpreter, estos son tus comandos esenciales:

| **Comando**                                             | **Función**                                                                                         |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `hostname`                                              | Muestra el nombre del equipo.                                                                       |
| `systeminfo`                                            | **El comando más completo.** Muestra SO, Build, Arquitectura, RAM y la lista de parches instalados. |
| `whoami`                                                | Muestra tu usuario actual                                                                           |
| `wmic qfe get Caption,Description,HotFixID,InstalledOn` | Lista detallada de actualizaciones y parches de seguridad (KBs).                                    |

>[!tip] **Consejo eJPTv2:** Si `systeminfo` devuelve demasiada información, puedes filtrar lo que buscas: `systeminfo | findstr /B /C:"<nombre_apartado>" /C:"<nombre_apartado>"...`

## ⚡ 3. Enumeración vía Meterpreter

Meterpreter facilita mucho las cosas con comandos integrados que consultan la API de Windows directamente:

- **`getuid`**: Muestra el nombre del usuario con el que corre el servidor de Meterpreter.
- **`sysinfo`**: Proporciona de forma inmediata el **Hostname**, **SO**, **Arquitectura** y el idioma del sistema.
- **`getprivs`**: Muestra los privilegios del token actual (busca privilegios peligrosos como `SeImpersonatePrivilege`).

---
**Navegación:**
- [[5.1 Post-Explotación Windows]]