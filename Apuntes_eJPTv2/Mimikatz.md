# 🐱 Mimikatz & Kiwi: Dumping de Credenciales
El objetivo principal es extraer los hashes de las cuentas de usuario (preferiblemente Administradores) para realizar ataques de [[Pass the Hash Attacks#Usando el módulo Kiwi ( Mimikatz )|Pass The Hash]] o crackeo offline.

> [!warning] Necesario ser `SYSTEM` para poder utilizar esta herramienta 
## 🛠️ Método A: Extensión Kiwi ([[Metasploit]])
Si ya tienes una sesión de **[[Meterpreter]]** con privilegios elevados (`SYSTEM` o Administrador), usar Kiwi es la forma más estable y discreta de trabajar.

### 1. Preparación

- **Cargar módulo:** `load kiwi`
- **Ver ayuda:** `? kiwi` o `help kiwi` para listar todos los comandos disponibles.

![[Imagenes/KiwiCommands.png]]
Comandos para extraer los hashes

|**Comando**|**Descripción**|
|---|---|
|**creds_all**|Intenta extraer todas las credenciales posibles de la memoria (SSP, Kerberos, etc.).|
|**lsa_dump_sam**|Extrae específicamente los hashes NTLM del archivo SAM (Security Account Manager).|
|**lsa_dump_secrets**|Extrae secretos de LSA (contraseñas de servicios, cuentas de equipo, etc.).|

- `creds_all`

![[Imagenes/creds_allKiwi.png]]

- `lsa_dump_sam`

![[Imagenes/NTLMHashKiwi.png]]

_(Seguir explorando resto de los Kiwi Commands)_

## 🛠️ Método B: Ejecutable `mimikatz.exe`
A veces necesitaremos ejecutar el binario directamente en la víctima si no tenemos Meterpreter o si queremos usar funciones muy específicas.
### 1. Transferencia y Ejecución

- **Ruta en Kali:** `/usr/share/windows-resources/mimikatz/x64/mimikatz.exe`
- **Subida:** Usa `upload` en Meterpreter o un servidor Python (`python3 -m http.server`) y descárgalo con `certutil`.

### 2. Secuencia de Comandos en la Víctima

Una vez dentro de la consola de mimikatz:

1. **Habilitar privilegios de depuración:** `privilege::debug`

> [!important] Si recibes el mensaje `Privilege '20' OK`, tienes permiso para interactuar con procesos del sistema.

![[Imagenes/PrivilegioMimikatz.png]]

2. **Extracción de Hashes (SAM):** `lsadump::sam` _Extrae los nombres de usuario y sus hashes NTLM directamente del registro._

![[Imagenes/lsadumpMimikatz.png]]

3. **Extracción de Secretos:** `lsadump::secrets` _Muestra información guardada en el registro, como contraseñas de servicios o tareas programadas._
4. **Extracción de Memoria (LOGONPASSWORDS):** `sekurlsa::logonpasswords` _Es el comando "mágico". Busca en el proceso LSASS contraseñas que aún residan en memoria (en sistemas antiguos pueden aparecer en texto plano, en modernos verás los hashes)._

## 🚩 Diferencia entre Comandos

- **lsadump:** Interactúa con los archivos de "disco" del sistema (SAM, SYSTEM, SECURITY).
- **sekurlsa:** Interactúa con la **memoria RAM** (proceso `lsass.exe`). Es más volátil pero suele contener credenciales de usuarios que tienen una sesión abierta en ese momento.

---

**Navegación:**
- Volver a: [[3.3 Obtención Credenciales en Windows]]
- [[Herramientas Post-Explotación Windows]]
- [[Herramientas]]