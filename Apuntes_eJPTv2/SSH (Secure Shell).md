# 🔑 SSH (Secure Shell) - Apuntes eJPTv2
El protocolo **SSH** es el estándar de oro para la administración remota segura de sistemas, principalmente en entornos **Linux**. A diferencia de FTP o Telnet, SSH cifra todo el tráfico, lo que lo hace un objetivo difícil pero extremadamente valioso.

## 🌐 Puerto de Funcionamiento

- **Puerto 22 (TCP):** Es el puerto estándar. Si lo encuentras abierto, casi siempre estarás ante un servidor **OpenSSH**.

## 🔍 Conceptos Críticos para el Pentester:

1. **Cifrado de Extremo a Extremo:**
    - A diferencia de FTP, no puedes simplemente "espiar" las contraseñas con Wireshark. El tráfico está cifrado, por lo que el ataque debe ser directo contra el servicio o mediante el robo de credenciales.
2. **Autenticación por Claves (Key-based):**
    - SSH permite usar pares de claves (Pública y Privada). Si durante tu fase de post-explotación en una máquina encuentras un archivo llamado `id_rsa` en la carpeta `.ssh/`, ¡PREMIO! Es la llave privada que te permite entrar sin contraseña.
3. **SSH Tunneling (Pivotaje):**
    - SSH es vital para el **Pivotaje**. Puedes usar una conexión SSH para "tunelizar" tu tráfico y atacar máquinas internas de una red a las que no tienes acceso directo.

> [!note] El punto 3 es una opción, pero en la eJPTv2, el [[Metasploit#🪜 Pivoting|Pivoting]] se hace a través de [[Metasploit]] 

## 🚀 Metodología de Ataque (eJPTv2):

- **Paso 1: Enumeración de Banner e Identificación**
    - Usa `nmap -sV -p 22 <IP>`. Aunque las versiones modernas de OpenSSH son muy seguras, versiones antiguas pueden tener vulnerabilidades de enumeración de usuarios.
- **Paso 2: Ataque de Fuerza Bruta (Brute Force)**
    - Si no tienes claves y el servicio permite login por contraseña, usa `Hydra`: `hydra -L usuarios.txt -P passwords.txt ssh://<IP> -t 4` _(Nota: Usa un número bajo de hilos `-t` para no bloquear el servicio o ser detectado rápidamente)._
- **Paso 3: Uso de Claves Robadas**
    - Si encuentras una clave `id_rsa`, descárgala a tu Kali y úsala así:
        1. `chmod 600 id_rsa` (Es obligatorio cambiar los permisos o SSH la rechazará).
        2. `ssh -i id_rsa <usuario>@<IP>`
- **Paso 4: Post-Explotación (Configuraciones Débiles)**
    - Una vez dentro, revisa `/etc/ssh/sshd_config`. Si ves `PermitRootLogin yes`, significa que el administrador cometió el error de permitir ataques directos al usuario `root`.

> [!tip] **Consejo:** SSH suele ser tu "punto de persistencia". Si logras entrar por una vulnerabilidad web, intenta añadir tu propia clave pública al archivo `~/.ssh/authorized_keys` de la víctima para tener una entrada siempre abierta y segura. [[Persistencia por Claves SSH]]


---
**Navegación:**
- Volver a: [[2.3 Servicios de Linux Frecuentemente Explotados]]
- Volver a: [[3.4 Servicios de Linux Frecuentemente Explotados]]