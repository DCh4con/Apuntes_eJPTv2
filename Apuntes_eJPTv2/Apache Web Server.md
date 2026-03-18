# 🐧 Apache HTTP Server
Como experto, debes ver a **Apache** como el estándar de oro de los servidores web en entornos **Linux**. Es extremadamente modular, lo que a veces introduce vulnerabilidades por malas configuraciones.

## 🌐 Puertos de Funcionamiento

- **Puerto 80 (HTTP):** Tráfico web en texto plano (sin cifrar). 
- **Puerto 443 (HTTPS):** Tráfico cifrado mediante certificados SSL/TLS.

## 📂 Estructura Crítica en el Sistema (Linux)

Para un Pentester, estas rutas son "oro":

- **Directorio Raíz Web:** `/var/www/html/` (Aquí es donde intentarás subir tu _Webshell_ en PHP).
- **Archivo de Configuración:** * Sistemas basados en Debian/Ubuntu: `/etc/apache2/apache2.conf`
    - Sistemas basados en RedHat/CentOS: `/etc/httpd/conf/httpd.conf`
- **Archivos `.htaccess`:** Pueden contener reglas de seguridad o redirecciones que podrías saltarte si están mal configurados.

## 🔍 Guía de Pentesting para Apache:

1. **Enumeración de Versión y SO:**
    - Apache suele ser muy "charlatán". Un simple `nmap -sV` te dirá no solo la versión de Apache, sino a menudo la distribución de Linux exacta (ej. _Apache/2.4.41 (Ubuntu)_).
2. **Identificación de Módulos:**
    - Busca módulos antiguos o peligrosos como `mod_cgi`. Si está activo y es vulnerable, podrías ejecutar el famoso exploit **[[Shellshock]]**.
3. **Búsqueda de Archivos Sensibles:**
    - Usa `Nikto`: Es una herramienta excelente para Apache que busca archivos de configuración por defecto, scripts de ejemplo y vulnerabilidades conocidas de forma automatizada.
    - Busca archivos `.php` que acepten parámetros (ej. `index.php?page=...`) para intentar ataques de **LFI (Local File Inclusion)** y leer el archivo `/etc/passwd`.
4. **Permisos de Directorio:**
    - Si al acceder a una carpeta ves el listado de archivos (_Index of /..._), revisa si hay copias de seguridad como `config.php.bak` o archivos `.zip`.


---
**Navegación:**
- Volver a: [[2.3 Servicios de Linux Frecuentemente Explotados]]
- Volver a: [[3.4 Servicios de Linux Frecuentemente Explotados]]