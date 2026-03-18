# 📂 FTP (File Transfer Protocol)
El protocolo **FTP** es un servicio clásico para la transferencia de archivos.

## 🌐 Puertos de Funcionamiento

- **Puerto 21 (Canal de Control):** Se usa para enviar comandos (autenticación, comandos `ls`, `cd`, etc.). Este el puerto que veremos abierto al usar `nmap`
- **Puerto 20 (Canal de Datos):** Se usa para la transferencia real de los archivos (en modo activo).

## 🔍 Conceptos Críticos para el Pentester:

1. **FTP Anonymous (Acceso Anónimo):**
    - **¡Regla de oro!** Siempre intenta loguearte con el usuario `anonymous` y la contraseña `anonymous` (o dejarla en blanco). Si el administrador no lo ha desactivado, tendrás acceso al sistema de archivos.
2. **Tráfico en Texto Plano:**
    - FTP no cifra nada. Si estás haciendo un ataque de **Man-in-the-Middle (MitM)** con herramientas como _Wireshark_, verás el usuario y la contraseña de cualquier persona que se conecte en claro.
3. **Versiones con Backdoor:**
    - Algunas versiones específicas de servidores FTP tienen vulnerabilidades famosas. Ejemplo: `vsftpd 2.3.4` tiene una puerta trasera que te da acceso _root_ si envías un usuario que termine en `:)`.

## 🚀 Metodología de Ataque (eJPTv2):

- **Paso 1: Enumeración de Banner**
    - Usa `nmap -sV -p 21 <IP>`. Identificar el software (ej. _vsftpd_, _ProFTPD_) es el primer paso para buscar exploits en [[searchsploit]].
- **Paso 2: Scripts de Nmap**
    - Ejecuta: `nmap --script ftp-anon -p 21 <IP>` para verificar automáticamente si permite el acceso anónimo.
- **Paso 3: Fuerza Bruta**
    - Si el acceso anónimo falla y no hay exploits conocidos, usa [[Hydra]] con una lista de usuarios comunes: `hydra -L users.txt -P passwords.txt ftp://<IP>`
- **Paso 4: Escalada a RCE (Ejecución de Comandos)**
    - Si tienes permisos de **escritura** (`put`) y ese servidor FTP comparte carpeta con el servidor web (Apache o IIS), sube una **Webshell**. Luego visítala desde el navegador y habrás tomado el control del servidor.

> [!CAUTION] **Nota:** Siempre revisa si puedes subir archivos (`PUT`). A veces no puedes leer archivos sensibles, pero si puedes subir un archivo `.ssh/authorized_keys`, podrías ganar acceso por SSH más tarde. [[Persistencia por Claves SSH]]


---
**Navegación:**
- Volver a: [[2.3 Servicios de Linux Frecuentemente Explotados]]
- Volver a: [[3.4 Servicios de Linux Frecuentemente Explotados]]