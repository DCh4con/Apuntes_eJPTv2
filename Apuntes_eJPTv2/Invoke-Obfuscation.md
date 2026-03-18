# 🎭 Invoke-Obfuscation: Evasión de PowerShell

**Invoke-Obfuscation** es un framework de ofuscación de comandos y scripts de PowerShell compatible con PowerShell v2.0+. Su objetivo principal es modificar el código de tal manera que mantenga su funcionalidad original pero su firma sea indetectable para soluciones de seguridad como **Windows Defender**.

## ⚙️ 0. Recursos
Video Tutorial como usar Invoke-Obfuscation
- https://www.youtube.com/watch?v=6xexyQwG7SY
- _(La primera parte muestra como usar esta herramienta, la segunda parte enseña a usar [[Shellter]])_

Proyecto: [Github-Invoke-Obfuscation](https://github.com/danielbohannon/Invoke-Obfuscation)
PayloadAllTheThings: [Reverse Shells](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#powershell) 

## ⚙️ 1. Instalación y Preparación
Dado que la herramienta es un módulo de PowerShell, necesitamos instalar el entorno de PowerShell en nuestro Kali Linux.

Instalar PowerShell en Kali Linux 
- `sudo apt-get install powershell`
 Clonar el repositorio oficial 
 - `git clone https://github.com/danielbohannon/Invoke-Obfuscation`
 - `cd Invoke-Obfuscation`
### Iniciar la Herramienta:

1. Entramos al entorno de PowerShell: `pwsh`
2. Importamos el módulo: `Import-Module ./Invoke-Obfuscation.psd1`
3. Ejecutamos: `Invoke-Obfuscation` _(Entramos en la herramienta)_

## 🚀 2. Flujo de Trabajo (Uso de la Herramienta)
Para el **eJPTv2**, el flujo típico consiste en tomar una **Reverse Shell** de PayloadAllTheThings y ofuscarla.
### Paso 1: Configurar el Script
Primero, crea un archivo `.ps1` con tu reverse shell, asegúrate de configurar tu IP de atacante y el puerto. Luego, cárgalo en la herramienta:
- `SET SCRIPTPATH /ruta/de/tu/script_shell.ps1`
### Paso 2: Seleccionar el Método de Ofuscación
La herramienta ofrece múltiples opciones. Según el path de eJPTv2, las más efectivas son:
1. **ENCODING:** Codifica los caracteres del script (Hex, Octal, Binary, etc.) para que no se lean cadenas de texto sospechosas como `New-Object` o `Net.Sockets`.
2. **AST (Abstract Syntax Tree):** * **Explicación:** Esta técnica manipula la estructura del árbol de sintaxis del script. En lugar de solo cambiar el texto, cambia cómo se construye el comando internamente. _(Oculta los nodos del comando de Powershell)_
    - **Efectividad:** Es la técnica que suele funcionar mejor para saltarse **Windows Defender**, ya que rompe los patrones lógicos que el antivirus busca.
### Paso 3: Generar y Copiar
Una vez seleccionada la técnica (puedes aplicar varias capas), la herramienta te mostrará el código resultante. Cópialo y crea un archivo `.ps1` para el siguiente paso.

## 📡 3. Ejecución y Recepción
1. **Preparar el Listener:** En tu Kali, abre un puerto para recibir la conexión.
	- `nc -lvnp <PUERTO>`
2. Transfiere el archivo `.ps1` ofuscado y ejecútalo.
	- `sudo python3 -m http.server 80`
	- Descárgalo en la máquina y ejecuta el archivo `.ps1`

## 🚩 Notas sobre AST y AMSI

- **¿Por qué usar AST?** Los antivirus modernos no solo buscan palabras prohibidas, analizan la "intención" del código. AST confunde este análisis cambiando el orden de las operaciones sin alterar el resultado final.
- **AMSI:** Windows Defender usa AMSI para escanear los scripts de PowerShell justo antes de ejecutarse (cuando ya están "desofuscados" en memoria). Por ello, a veces es necesario combinar esta herramienta con un **AMSI Bypass** previo.

--- 
**Navegación:**
- [[Técnicas]]
- [[Herramientas]]