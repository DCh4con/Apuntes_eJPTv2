# 🐧 Post-Explotación: Enumeración de Sistema Linux
El objetivo es obtener una visión clara de la infraestructura del sistema operativo. En Linux, la mayoría de la información crítica reside en archivos de texto dentro del directorio `/etc/`.

## 🎯 1. ¿Qué buscamos?

- **Identidad:** Nombre del host y su propósito en la red.
- **Distribución:** ¿Es Ubuntu, Debian, CentOS, RedHat? (Esto cambia los comandos de gestión de paquetes).
- **Kernel:** Punto vital. Muchos exploits de elevación de privilegios dependen exclusivamente de la versión del Kernel.
- **Hardware:** Arquitectura (x86, x64, ARM) y almacenamiento disponible.
- **Software:** ¿Qué programas están instalados? ¿Hay compiladores como `gcc` disponibles?

## 💻 2. Comandos de Enumeración (Nativos)
Utiliza estos comandos para recolectar inteligencia rápidamente desde cualquier shell:
### 🖥️ Información del Sistema
|**Comando**|**Función**|
|---|---|
|`hostname`|Muestra el nombre del equipo.|
|`cat /etc/issue`|Muestra la distribución y versión de forma rápida.|
|`cat /etc/*release`|Información detallada sobre la distribución y el sistema operativo.|
|`uname -a`|**Crucial:** Muestra versión del Kernel, arquitectura y hostname.|
|`uname -r`|Muestra específicamente la versión del Kernel (ideal para buscar exploits).|
|`lscpu`|Detalles de la arquitectura de la CPU.|
### 📂 Almacenamiento y Entorno
|**Comando**|**Función**|
|---|---|
|`df -h`|Muestra el espacio en disco y sistemas de archivos montados (en formato humano).|
|`lsblk`|Lista de discos y particiones disponibles (incluyendo los no montados).|
|`env`|Muestra las variables de entorno. Revisa el `PATH` para ver dónde busca binarios el sistema.|
|`cat /etc/shells`|Lista las shells instaladas (bash, sh, zsh, etc.).|
### 📦 Software y Paquetes

| **Comando** | **Función**                                                          |
| ----------- | -------------------------------------------------------------------- |
| `dpkg -l`   | (Debian/Ubuntu) Lista todos los paquetes instalados y sus versiones. |
| `rpm -qa`   | (CentOS/RHEL) Lista todos los paquetes instalados.                   |
## ⚡ 3. Enumeración vía Meterpreter
Si tu sesión es de Meterpreter (Python o PHP), tienes comandos integrados:

- **`sysinfo`**: Muestra el SO, el Kernel y la arquitectura.
- **`getuid`**: Muestra el ID del usuario actual.
- **`shell`**: Te permite caer en una shell de sistema para ejecutar los comandos de la tabla anterior.


## 💡 4. Tips para el eJPTv2

1. **Kernel Exploits:** Si ves un Kernel muy antiguo (ej: `2.6.x` o `3.x`), anótalo. Herramientas como [[Linux Exploit Suggester]] lo usarán como referencia.
2. **Binarios Interesantes:** Al revisar `env`, fíjate si hay rutas inusuales en el `PATH`. Esto podría indicar software personalizado que sea vulnerable.
3. **Compiladores:** Siempre comprueba si existe `gcc` o `cc` instalados (`which gcc`). Esto te dirá si puedes compilar exploits directamente en la víctima o si debes hacerlo en tu Kali ([[Compilación Exploits|compilación cruzada]]).

---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]