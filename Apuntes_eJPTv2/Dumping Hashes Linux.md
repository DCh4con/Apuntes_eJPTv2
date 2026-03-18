# 🔑 Dumpeo de Hashes y Cracking de Contraseñas (Linux)
En Linux, mientras que `/etc/passwd` es legible por todos los usuarios, las contraseñas cifradas (hashes) se guardan en `/etc/shadow`, un archivo que solo puede leer el usuario **root**.

## 🛰️ 1. Extracción de Hashes (Dumpeo)
Para obtener los hashes, primero debemos haber escalado privilegios a **root**.
### Método A: Extracción Manual (Shell)
Si ya eres root, simplemente visualiza y copia el contenido:
- `cat /etc/shadow`
- Cada línea sigue el formato `usuario:$6$salsh$hash...`. El número (ej. `$6$`) indica el algoritmo (en este caso SHA-512).

### Método B: Metasploit (Post-Explotación)
Si tienes una sesión de Meterpreter, puedes automatizar la recolección de todos los hashes:
- `use post/linux/gather/hashdump`
- `set SESSION <ID>`
- `run`
- Este módulo extraerá los hashes y los guardará en la base de datos de Metasploit o los mostrará por pantalla.

## 🛠️ 2. Cracking (Unshadow)
Antes de usar John the Ripper, es recomendable combinar los archivos `/etc/passwd` y `/etc/shadow` en un solo archivo para que la herramienta tenga el contexto completo de los usuarios.

**En tu Kali Linux:**
- `unshadow passwd_recuperado.txt shadow_recuperado.txt > hashes_para_crackear.txt`

Con el archivo `hashes_para_crackear.txt` podemos usarlo para usar [[John The Ripper]] o [[Hashcat]]
## ⚡ 3. Cracking de Contraseñas
Una vez tenemos el archivo de hashes, utilizamos herramientas de fuerza bruta/diccionario.
### Con John the Ripper
Es la herramienta más sencilla para ataques rápidos:
Ej pequeño con wordlist de rockyou:
- `john --wordlist=/usr/share/wordlists/rockyou.txt hashes_para_crackear.txt`
- _Para ver las contraseñas encontradas después del proceso:_ `john --show hashes_para_crackear.txt`

## 💡 Tips para el eJPTv2

1. **Prioridad de Usuarios:** No pierdas tiempo crackeando usuarios de servicio (`bin`, `sys`, `daemon`). Céntrate en el usuario **root** y en los usuarios con carpetas en `/home` (UID > 1000).
2. **Rockyou.txt:** Es el diccionario estándar para el examen. Asegúrate de tenerlo descomprimido en tu Kali (`gunzip /usr/share/wordlists/rockyou.txt.gz`).
3. **Hashes Vacíos:** Si ves algo como `usuario:*:...` o `usuario:!:...`, significa que la cuenta está bloqueada o no tiene contraseña configurada para login interactivo.

#
---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]