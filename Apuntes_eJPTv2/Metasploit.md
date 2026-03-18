# 🚀 Metasploit Framework (MSF)
**Metasploit Framework** es un proyecto de código abierto para pruebas de penetración que proporciona una infraestructura robusta para automatizar todo el ciclo de vida de un pentesting.
Más que una herramienta, es un entorno modular que permite automatizar desde la enumeración inicial hasta la post-explotación y persistencia.
## 📍Introducción
### 🧩 Conceptos Clave

| **Término**        | **Definición**                                                      |
| ------------------ | ------------------------------------------------------------------- |
| **Módulo**         | Pieza de código específica para una tarea (exploit, scanner, etc.). |
| **Vulnerabilidad** | El fallo o "agujero" de seguridad en el objetivo.                   |
| **Exploit**        | Código que aprovecha una vulnerabilidad específica.                 |
| **Payload**        | Código que se ejecuta en el objetivo tras la explotación.           |
| **Listener**       | Utilidad que espera y gestiona las conexiones entrantes             |
### 🏗️ Arquitectura de Módulos
La potencia de MSF reside en su **modularidad**: no necesitas reescribir código para añadir nuevas funciones, solo añades un módulo a la estructura.
#### Tipos de Módulos

- **Exploits:** Utilizados para aprovechar una vulnerabilidad. Siempre van acompañados de un payload.
- **Payloads:** El código que se ejecuta tras el éxito (ej. Meterpreter).
- **Auxiliary:** Escáneres de puertos, sniffers, fuzzers y módulos de enumeración (no lanzan payloads).
- **Post:** Módulos para ejecutar tras comprometer el sistema (recolección de hashes, enumeración local, etc.).
- **Encoders:** Ayudan a evadir firmas de Antivirus codificando el payload.
- **NOPs:** Generan instrucciones de "No Operación" para mantener la estabilidad del tamaño del payload.
### ⚡ Payloads: Staged vs. Non-Staged
Un aspecto crítico de la estabilidad del ataque:

- **No Staged (Singles):** El payload completo viaja en una sola pieza con el exploit. Es más ruidoso pero más simple.
	-  Sintaxis: `windows/meterpreter_reverse_tcp` Nótese el guion bajo `_`_(Entre `meterpreter` y `reverse_tcp`)_.
- **Staged:** Funciona en dos tiempos:
    1. **Stager:** Código pequeño que establece la comunicación inicial.
    2. **Stage:** La parte pesada (ej. Meterpreter) que el stager descarga después.
    - Sintaxis: `windows/meterpreter/reverse_tcp` Nótese la barra `/` _(Entre `meterpreter` y `reverse_tcp`)_.

### 🚀 Flujo de Pentesting con MSF
| Fase                        | Implementación MSF                     |
| --------------------------- | -------------------------------------- |
| Información y Enumeración   | Módulos Auxiliares                     |
| Escaneo de Vulnerabilidades | Módulos Auxiliares y Nessus            |
| Explotación                 | Módulos Exploit y Payloads             |
| Post-Explotación            | Meterpreter                            |
| Escalada de Privilegios     | Módulos Post-Explotación y Meterpreter |
| Persistencia                | Módulos de Persistencia                |

### 📂 Directorios Importantes
- **Módulos del Sistema:** `/usr/share/metasploit-framework/modules`
- **Módulos Propios (Usuario):** `~/.msf4/modules` (Aquí puedes añadir tus propios scripts o exploits descargados).
## 🗄️ Gestión de Base de Datos (BBDD)
Metasploit utiliza **PostgreSQL** para almacenar toda la información recolectada durante un pentesting (hosts, servicios, vulnerabilidades, credenciales y notas). Esto permite realizar un seguimiento organizado y profesional de la auditoría.
Como estándar, esta bbdd suele estar en el ``puerto 5432``
### 🛠️ 1. Gestión del Servicio (Systemd)

Antes de interactuar con la base de datos desde MSF, el servicio de PostgreSQL debe estar corriendo en el sistema operativo.

- **Iniciar:** `sudo systemctl start postgresql`
- **Reiniciar:** `sudo systemctl restart postgresql`
- **Parar:** `sudo systemctl stop postgresql`
- **Ver estado:** `sudo systemctl status postgresql`
- **Habilitar al arranque:** `sudo systemctl enable postgresql`
### ⚙️ 2. Herramienta `msfdb`
Es el script de gestión simplificada para la base de datos de Metasploit desde la terminal de Linux.

|**Comando**|**Acción**|
|---|---|
|**msfdb init**|Inicia y configura la base de datos por primera vez.|
|**msfdb start**|Inicia el servicio de la base de datos.|
|**msfdb stop**|Detiene el servicio.|
|**msfdb status**|Comprueba si el servicio está activo y conectado.|
|**msfdb run**|**(Recomendado)** Inicia la BBDD y lanza `msfconsole` automáticamente.|
|**msfdb reinit**|Borra todo y vuelve a inicializar (Cuidado: borra datos previos).|
|**msfdb delete**|Elimina la base de datos permanentemente.|

### 💻 3. Comandos `db_` (Dentro de msfconsole)
Una vez dentro de la consola de Metasploit, puedes gestionar la conexión y los datos con los siguientes comandos:

| **Comando**                     | **Función**                                                                        |
| ------------------------------- | ---------------------------------------------------------------------------------- |
| **db_status**                   | Verifica si MSF está conectado a la base de datos PostgreSQL.                      |
| **db_connect**                  | Conecta manualmente a una base de datos (requiere credenciales/ruta).              |
| **db_disconnect**               | Desconecta de la base de datos actual.                                             |
| **db_import /ruta/archivo.xml** | Importa archivos de resultados (Nmap XML, Nessus, Burp, etc.).                     |
| **db_export /ruta/archivo.xml** | Exporta el contenido de la base de datos a un archivo (formato XML).               |
| **db_remove**                   | Elimina una entrada específica de la base de datos.                                |
| **db_save**                     | Guarda el estado actual de la base de datos.                                       |
| **db_stats**                    | Muestra estadísticas (cuántos hosts, servicios y credenciales hay).                |
## ⌨️ Guía de Comandos Básicos: Msfconsole
La `msfconsole` es la interfaz principal de Metasploit. Para operarla con fluidez, es necesario entender el flujo: **Buscar -> Seleccionar -> Configurar -> Ejecutar**.
### 🛠️ 1. Comandos Generales

- `version`: Muestra la versión actual del framework.
- `help` / `?`: Abre el menú de ayuda.
- `clear`: Limpia la terminal.
- `exit`: Cierra la consola.
- `back`: Sale del contexto del módulo actual para volver al "root" de la consola.
### 🔍 2. El Comando `search` (Búsqueda Avanzada)
No pierdas tiempo navegando por carpetas; usa filtros para encontrar el módulo exacto.
### Filtros por Tipo y Plataforma

- `search type:exploit`: Solo busca vulnerabilidades explotables.
- `search platform:windows`: Filtra por sistema operativo.
- `search platform:linux`: Filtra para entornos Linux.

### Filtros Técnicos y de Calidad

- `search cve:2017-0144`: Busca por un código de vulnerabilidad específico.
- `search rank:excellent`: Muestra solo los módulos más estables y fiables.
- `search date:2023`: Busca módulos publicados en un año concreto.

> [!tip] **Búsqueda Combinada:** `search type:exploit platform:windows rank:excellent smb` (Busca exploits de SMB para Windows que sean de alta calidad).

### 🎯 3. Selección y Configuración de Módulos
Una vez identificado el exploit, hay que prepararlo para el ataque.
#### Selección

- `use <ruta_o_id>`: Selecciona el módulo (ej: `use exploit/windows/smb/ms17_010_eternalblue` o `use 0`).
- `info`: Muestra descripción, autor, referencias (CVE) y qué hace exactamente el módulo.
#### Configuración (Variables)
El comando `show options` es el más importante aquí. Te dirá qué campos son **Required** (Obligatorios).

- `set RHOSTS <IP>`: Define la IP de la víctima (Remote Host).
- `set LHOST <IP>`: Define tu IP (Local Host) para recibir la conexión.
- `setg LHOST <IP>`: (**Global**) Establece tu IP para todos los módulos que uses en la sesión.
- `unset <variable>`: Borra el valor de una variable.
- `show advanced`: Muestra opciones avanzadas y parámetros técnicos profundos.
### 🐚 4. Gestión de Payloads
El payload es la "carga útil" (qué obtendrás tras entrar).

- `show payloads`: Lista solo los payloads compatibles con el exploit que has seleccionado.
- `set PAYLOAD <ruta>`: Define qué quieres recibir (ej: `set PAYLOAD windows/x64/meterpreter/reverse_tcp`).

| **Payload**                       | **Uso común**                           |
| --------------------------------- | --------------------------------------- |
| `windows/meterpreter/reverse_tcp` | Control total (Meterpreter) en Windows. |
| `cmd/unix/reverse_bash`           | Shell básica en Linux/Unix.             |
| `windows/x64/shell/reverse_tcp`   | Shell clásica (CMD) en Windows 64 bits. |
### 🚀 5. Ejecución y Sesiones
#### Lanzar el ataque

- `exploit` o `run`: Inicia el proceso de explotación.
- `exploit -j`: Ejecuta el ataque como un "Job" en segundo plano.
#### Gestionar las conexiones
Una vez que el ataque tiene éxito, se abre una **sesión**.

- `sessions`: Lista todas las máquinas comprometidas activas.
- `sessions <ID>`: Entrar a una sesión.
- `sessions -i <ID>`: Interactúa con una sesión específica para empezar a dar comandos.
- `background` (o `Ctrl+Z`): Sale de una sesión activa pero **la mantiene abierta** en segundo plano.
- `sessions -n <nombre> -i <ID>` : Darle un nombre a una sesión
- `sessions -u <ID>`: Intenta mejorar la sesión "normal" a una sesión con Meterpreter lanzando automáticamente el módulo `post/multi/manage/shell_to_meterpreter` 
- `sessions -k <ID>`: Mata (cierra) una sesión específica.
- `sessions -K`: Mata (cierra) todas las sesiones.
## 📂Creación y Manejo de Workspaces
Los **Workspaces** (espacios de trabajo) permiten segmentar la base de datos de Metasploit. Son contenedores lógicos que aíslan la información recopilada (evidencia, hosts, servicios) por proyecto, cliente, red, etc. _(Depende de como queramos)_
### 🛠️ Comandos de Gestión
La gestión de los espacios de trabajo se realiza íntegramente con el comando `workspace`.
#### 1. Visualización y Ayuda

- **Listar workspaces:** `workspace`
    - El espacio actual aparecerá marcado con un asterisco (`*`).
    - Por defecto, Metasploit utiliza el workspace llamado `default`.
- **Ayuda detallada:** `workspace -h`
#### 2. Manipulación de Espacios

| **Acción**        | **Comando**                                                    |
| ----------------- | -------------------------------------------------------------- |
| **Crear**         | `workspace -a <nombre>`                                        |
| **Cambiar**       | `workspace <nombre>`                                           |
| **Eliminar**      | `workspace -d <nombre>`                                        |
| **Renombrar**     | `workspace -r <nombre_actual> <nuevo_nombre>`                  |

## 🔍 Módulos de Escáner de Hosts
**Módulo:** `auxiliary/scanner/discovery/arp_sweep

- `set RHOSTS <Red>/<Máscara>`: Indicamos la red a escanear.
- `set THREADS <value>`: Indicamos el número de hilos para escanear la red.
### 📊 Uso de Nmap dentro de Metasploit
Podemos usar [[Nmap]] dentro del framework de Metasploit, usando `db_nmap`.
Así, todos los hosts que descubramos, se guardará automáticamente dentro de la BBDD de Metasploit.
- **Comando**: `db_nmap -sn <Red>/<Máscara>`
## 🔍 Módulos de Escáner de Puertos
Estos módulos son útiles si no puedes usar Nmap externamente o si quieres que los resultados se integren directamente en el flujo de trabajo de Metasploit.
**Categoría:** `auxiliary/scanner/portscan/`

| **Módulo** | **Descripción**             | **Equivalente Nmap**                             |
| ---------- | --------------------------- | ------------------------------------------------ |
| **/ack**   | Firewall Scanner (ACK)      | [[Nmap#⚙️ 3. Tipos de Escaneo (Técnicas)\| -sA]] |
| **/syn**   | TCP SYN Stealth (Half-open) | [[Nmap#⚙️ 3. Tipos de Escaneo (Técnicas)\| -sS]] |
| **/tcp**   | TCP Connect Scan            | [[Nmap#⚙️ 3. Tipos de Escaneo (Técnicas)\| -sT]] |
| **/xmas**  | TCP "XMas" Scanner          | [[Nmap#⚙️ 3. Tipos de Escaneo (Técnicas)\| -sX]] |
**Categoría:** `auxiliary/scanner/discovery/`
- **/udp_sweep**: Escáner para descubrimiento de servicios UDP comunes.

>💡 **Recordatorio:** No olvidar configurar los `OPTIONS` (`RHOSTS`, `THREADS`, etc.) en cada módulo mediante el comando `show options` y `set`.

### 📊 Uso de Nmap dentro de Metasploit
Podemos usar [[Nmap]] dentro del framework de Metasploit, usando `db_nmap`.
Así, toda la información que obtengamos (puertos abiertos, servicios, versiones, etc.), se guardará automáticamente dentro de la BBDD de Metasploit. Los parámetros siguen siendo los mismos:
- **Comando:** `db_nmap [parámetros_de_nmap] <IP>`
- **Ej**: `db_nmap -sS -sV -O <IP>`

> [!tip] Recomendable crear un [[Metasploit#📂 Metasploit Creación y Manejo de Workspaces|workspace]] para cada host, así los datos estarán mejor organizados en la BBDD

## 🛠️ Módulos Enumeración de Servicios

> [!note] Los siguientes **Módulos Básicos** son los practicados en el path de eJPTv2. Existen muchos mas para usar.
### 📂 FTP (File Transfer Protocol)
**Puerto:** 21 | **Categoría:** `auxiliary/scanner/ftp/`
#### Módulos Básicos
|**Módulo**|**Descripción**|
|---|---|
|**ftp_version**|Identifica banner y versión del servicio.|
|**ftp_login**|Fuerza bruta de credenciales (User/Pass).|
|**anonymous**|Verifica si permite acceso sin pass y permisos (R/W).|

### 📁 SMB (Server Message Block)
Puerto 445. Sin embargo, originalmente, SMB se ejecutaba sobre NetBIOS utilizando el puerto 139.
**Categoría:** `auxiliary/scanner/smb/`

> SMB es el servicio de Windows. SAMBA en la implementación de SMB en Linux
#### Módulos Básicos
|**Módulo**|**Descripción**|
|---|---|
|**smb_version**|Versión del protocolo y Sistema Operativo.|
|**smb_enumusers**|Enumeración de usuarios vía SAM RPC.|
|**smb_enumshares**|Lista recursos compartidos y permisos de acceso.|
|**smb_login**|Fuerza bruta o prueba de credenciales específicas.|

### 🌐 Web Server - HTTP (HyperText Transfer Protocol)
**Puerto:** 80 | **Categoría:** `auxiliary/scanner/http/`

> En caso de ser HTTPS, corre por el puerto 443
#### Módulos Básicos
Categoría: auxiliary/scanner/http/
##### General
| **Módulo**       | **Descripción**                                      |
| ---------------- | ---------------------------------------------------- |
| **http_version** | Análisis de banner para detectar software y versión. |
| **http_header**  | Captura metadatos y configuración de cabeceras.      |
| **http_login**   | Fuerza bruta contra autenticación básica HTTP.       |
| **robots_txt**   | Busca directorios ocultos indexados en `robots.txt`. |
| **dir_scanner**  | Fuerza bruta de directorios mediante diccionarios.   |
| **files_dir**    | Búsqueda de archivos con extensiones comunes.        |
##### Apache
- **apache_userdir_enum**: Enumeración de usuarios del sistema mediante el módulo UserDir.
### 📊 MySQL (Structure Query Language)
**Puerto:** 3306 | **Categoría:** `auxiliary/scanner/mysql/`
#### Módulos Básicos
| **Módulo**           | **Descripción**                                                    |
| -------------------- | ------------------------------------------------------------------ |
| **mysql_version**    | Confirma estado del servicio y versión.                            |
| **mysql_login**      | Fuerza bruta o acceso con credenciales por defecto.                |
| **mysql_enum**       | Lista BDs, usuarios, privilegios y variables.                      |
| **mysql_sql**        | Ejecución de consultas personalizadas (requiere credenciales).     |
| **mysql_schemadump** | Extrae la estructura de tablas y columnas (requiere credenciales). |
| **mysql_hashdump**   | Extrae hashes de la tabla `mysql.user` para cracking.              |
| **mysql_file_enum**  | Lista archivos locales usando `LOAD_FILE` (requiere credenciales). |

### 🔑 SSH (Secure Shell)
**Puerto:** 22 | **Categoría:** `auxiliary/scanner/ssh/`
#### Módulos Básicos
|**Módulo**|**Descripción**|
|---|---|
|**ssh_version**|Captura banner y versión del software.|
|**ssh_login**|Fuerza bruta de contraseñas.|
|**ssh_login_pubkey**|Prueba de acceso mediante claves privadas (`id_rsa`).|
|**ssh_enumusers**|Enumeración de usuarios por análisis de tiempos/errores.|

### 📧 SMTP (Simple Mail Transfer Protocol)
**Puerto:** 25 | **Categoría:** `auxiliary/scanner/smtp/`
#### Módulos Básicos
|**Módulo**|**Descripción**|
|---|---|
|**smtp_version**|Identifica el software del servidor de correo.|
|**smtp_enum**|Descubre usuarios mediante comandos `VRFY`, `EXPN` o `RCPT TO`.|

## 🕵️ Módulos de Post-Explotación (Gathering)
Los módulos de tipo `post` permiten automatizar la recolección de información crítica una vez que ya hemos comprometido un sistema. Estos módulos requieren una **SESSION** activa para ejecutarse.

> [!note] Los siguientes **Módulos** son los practicados en el path de eJPTv2 para la recolección de información de sistemas en la etapa de postexplotación.

### 🪟 Post-Explotación en Windows
|**Módulo**|**Descripción**|
|---|---|
|**`post/windows/gather/win_privs`**|Lista los privilegios actuales del usuario (útil para ver si podemos usar `getsystem`).|
|**`post/windows/gather/enum_logged_on_users`**|Muestra qué usuarios tienen sesiones activas (ideal para ataques de tokens).|
|**`post/windows/gather/checkvm`**|Identifica si el objetivo es una máquina virtual (VirtualBox, VMware, Hyper-V).|
|**`post/windows/gather/enum_applications`**|Lista software instalado y versiones (para buscar exploits locales).|
|**`post/windows/gather/enum_av_excluded`**|Revela carpetas que el Antivirus no escanea (ideal para esconder payloads).|
|**`post/windows/gather/enum_computer`**|Indica si la máquina pertenece a un **Dominio** o a un Workgroup.|
|**`post/windows/gather/enum_patches`**|Lista KBs de Windows instalados (para saber qué vulnerabilidades faltan).|
|**`post/windows/gather/enum_shares`**|Enumera recursos compartidos en la red (SMB).|
|**`post/windows/gather/enable_rdp`**|Comprueba si RDP está activo y permite habilitarlo remotamente.|
### 🐧 Post-Explotación en Linux
En Linux, la recolección se centra en archivos de configuración, redes y posibles contenedores.
### 🛡️ Sistema y Protecciones

- **`post/linux/gather/enum_system`**: Información general del SO, kernel y hardware.
- **`post/linux/gather/enum_protections`**: Busca configuraciones de ASLR, DEP y endurecimiento del sistema.
- **`post/linux/gather/enum_configs`**: Extrae archivos de configuración sensibles de `/etc/`.
### 🌐 Red y Entorno

- **`post/linux/gather/enum_network`**: Tablas de rutas, interfaces, conexiones activas y DNS.
- **`post/multi/gather/env`**: Muestra las variables de entorno (puede contener API keys o rutas).
### 🐳 Virtualización y Usuarios

- **`post/linux/gather/checkvm`**: Detecta si el Linux es virtualizado.
- **`post/linux/gather/checkcontainer`**: Determina si estamos dentro de un contenedor **Docker** o LXC.
- **`post/linux/gather/enum_users_history`**: Lee archivos `.bash_history` (puede contener contraseñas tecleadas por error).
### 🛠️ Gestión y Ejecución

- **`post/multi/manage/system_session`**: Información sobre la sesión actual del sistema.
- **`post/linux/manage/download_exec`**: Descarga un binario desde una URL y lo ejecuta en la víctima (útil para lanzar exploits de kernel).
## 📊 Consulta de Información Recopilada
Una vez que has ejecutado escaneos (como `db_nmap`) o exploits, o has importado un XML, la información se organiza en tablas. Estos comandos te permiten consultar esa "inteligencia" de forma estructurada.
### 0. Ayuda
En los siguientes comandos, puedes ver los parámetros explicados con -h
- `<comando> -h`
### 1. `hosts` (Gestión de Objetivos)
Muestra todas las máquinas descubiertas en el workspace actual.
### 2. `services` (Mapeo de Puertos)
Muestra los servicios encontrados en cada host.
### 3. `vulns` (Registro de Vulnerabilidades)
Almacena las vulnerabilidades confirmadas (ya sea por escaneos de `Nessus` importados o por módulos de `comprobacion` de Metasploit).
- Ej: `auxiliary/scanner/smb/smb_ms17_010` 
- Este módulo solo comprueba si el servicio smb descubierto es vulnerable a `eternalblue`. Si es vulnerable, creará un registro en bbdd que podremos ver con `vulns`
### 4. `creds` (El Botín de Credenciales)
Es el comando más satisfactorio. Almacena todos los usuarios, contraseñas y hashes obtenidos mediante ataques o extraídos de bases de datos.
### 5. `loot` (Archivos Expropiados)
Cuando usas un módulo post-explotación para robar archivos (como `/etc/shadow`, archivos de configuración o certificados), se guardan aquí.
### 6. `notes` (Información Técnica Adicional)
Muestra metadatos e información de inteligencia recolectada automáticamente por los módulos de escaneo o explotación. Es vital para el **fingerprinting** profundo del objetivo.
### 7. `analyze` (El "Asesor" de Metasploit)
Este es un comando potente que pocos usan. Analiza los datos de la base de datos y **te sugiere qué módulos usar** contra los hosts que ya conoces.

- **`analyze <IP>`**: Examina un host específico y busca exploits compatibles en la base de datos de MSF.
- **`analyze`**: Analiza todo el workspace actual.

## 🪜 Pivoting
El **Pivoting** ocurre cuando tomamos el control de un sistema (Host A) y descubrimos que tiene **dos o más interfaces de red**. Una interfaz mira hacia nosotros y la otra mira hacia una red interna privada (Host B) a la que no tenemos acceso directo.

> [!note] En la eJPTv2, el Pivoting se hace a través de Metasploit, por eso se explica aquí
### 🔍 Fase 0: Identificación
Una vez que tengas tu sesión de **Meterpreter**, lo primero es investigar dónde estás metido:
- `ipconfig` 
- Si ves dos segmentos de red distintos (ej: `10.10.10.5` y `192.168.1.20`), ¡tienes un candidato para hacer Pivot!
### 🛠️ Método 1: MSF Routing (Interno de Metasploit)
Este método permite que **cualquier módulo de Metasploit** (scanners, exploits) pueda "ver" la red interna a través de tu sesión abierta.
#### 1. Configurar la ruta
Desde la sesión de Meterpreter:
- `run autoroute -s 192.168.1.0/24`
	- Esto le dice a Metasploit: "Si envío algo a la 192.168.1.x, pásalo por esta sesión".
- `run autoroute -p`
	- Para verificar las rutas agregadas
- `run autoroute -d -s 192.168.1.0/24`
	- Para eliminar una ruta

Desde la consola principal de Metasploit
- `route add <subnet> <netmask> <session_id>`
- Podrás ver las rutas definidas con el comando `route`

Desde módulo `post/multi/manage/autoroute`
- `set NETMASK <netmask>`
- `set SESSION <id_session>`
- `set SUBNET <subnet>`
#### 2. Ej: Escaneo de la Red Interna
Ahora puedes usar módulos auxiliares de MSF para buscar otros equipos en la red interna:
- ``use auxiliary/scanner/discovery/arp_sweep``
- ``set RHOSTS 192.168.1.0/24``
- ``run``
### 🚪 Método 2: Port Forwarding (Túnel específico)
Útil cuando quieres traer un puerto de una máquina interna (Host B) a tu propia máquina local (Kali).
**Escenario:** El Host B (192.168.1.50) tiene un servidor web en el puerto 80 que no puedes ver.
En Meterpreter:
- `portfwd add -l 8080 -p 80 -r 192.168.1.50
	- **`-l 8080`**: Tu puerto local (en Kali).
	- **`-p 80`**: El puerto remoto en la víctima final.
	- **`-r 192.168.1.50`**: La IP de la víctima en la red interna.

**Resultado:** Si abres el navegador en tu Kali y vas a `http://127.0.0.1:8080`, estarás viendo el sitio web del Host B (Te has traído su puerto 80 a tu puerto 8080).
### 🌐 Método 3: ProxyChains (Para herramientas externas)
Si quieres usar herramientas fuera de Metasploit (como `nmap` o un navegador) para atacar la red interna, necesitas un túnel SOCKS.
#### 1. Levantar el servidor Proxy
En Metasploit:

- ``use auxiliary/server/socks_proxy``
- ``set SRVHOST 127.0.0.1``
- ``set SRVPORT 9050``        # Puerto estándar de ProxyChains
- ``set VERSION 4a``          # Versión común en laboratorios eJPT
- ``run -j``
#### 2. Configurar ProxyChains en tu Kali
Asegúrate de que el archivo `/etc/proxychains4.conf` tenga la misma IP y puerto al final del archivo: 
`socks4 127.0.0.1 9050`
#### 3. Ejecutar herramientas
Ahora "envuelve" tus comandos con `proxychains`:
- Ej: `proxychains nmap -sT -Pn -n 192.168.1.50`

>[!warning] **Importante:** ProxyChains solo soporta tráfico **TCP**. No puedes hacer escaneos UDP o ICMP (ping) a través de él. Por eso usamos el flag `-sT` (Connect Scan) en Nmap.
#### 4. Navegador
Si el Host B está levantando un servidor web, tendrás que configurar el navegador para que funcione como un proxy
Ejemplo con Firefox:
- `Abrir Settings -> General -> Network Settings (Esta opción esta abajo del todo en la pestaña General)`

![[Imagenes/FirefoxProxychainsConf.png]]

## 🎨 GUI para Metasploit: Armitage
**Armitage** es una **interfaz gráfica de usuario (GUI)** que actúa como un front-end para el Metasploit Framework (muy intuitiva) que visualiza los objetivos, recomienda exploits y expone las funcionalidades avanzadas del framework mediante menús desplegables. Es ideal para entender la topología de la red y gestionar múltiples sesiones simultáneamente.
### 🛠️ Cómo iniciar Armitage
Armitage no funciona solo; necesita conectarse al servicio de base de datos de Metasploit.

1. **Iniciar el servicio de base de datos:**
	- `sudo systemctl start postgresql`
2. **Iniciar el servidor de Metasploit (MSFRPC):** Armitage lo suele iniciar automáticamente, pero puedes lanzarlo con:
	- `armitage`
	- ⚠️ No suele venir instalado en Kali Linux ⚠️
	- `sudo apt-get install armitage`
3. **Conexión:** Al abrirse, aparecerá una ventana de conexión. Por defecto, los datos suelen ser:
	- **Host:** `127.0.0.1`
	- **Port:** `55553`
	- **User:** `msf`
	- **Pass:** _(La que hayas configurado o la que viene por defecto)_.
### ⚖️ Ventajas y Desventajas

| **Ventajas**                                                                         | **Desventajas**                                                                         |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| **Intuitivo:** Ideal para aprender el flujo de un pentest.                           | **Consumo de recursos:** Consume mucha más RAM que la consola.                          |
| **Gestión de Sesiones:** Cambiar entre 10 máquinas comprometidas es mucho más fácil. | **Inestabilidad:** A veces puede cerrarse inesperadamente en redes muy grandes.         |
| **Pivoting Visual:** Facilita mucho la configuración de rutas y túneles.             | **Ruidoso:** Algunas funciones automáticas pueden ser detectadas fácilmente por un IDS. |

#
---
**Navegación:**
- Relacionado: [[Msfvenom]]
- [[Herramientas]]