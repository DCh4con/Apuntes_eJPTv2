# 🛡️ Microsoft IIS (Internet Information Services)
Es el servidor web extensible de Microsoft que corre exclusivamente en Windows. Permite servir sitios web, aplicaciones (ASP.NET, PHP) y servicios (FTP, SMTP).

- **Puertos comunes:** 80 (HTTP) / 443 (HTTPS)
### 📁 Estructura que DEBES conocer:

- **Directorio raíz por defecto:** `C:\inetpub\wwwroot` (Aquí vive el contenido web).
- **Archivo de configuración clave:** `web.config` (Suele contener cadenas de conexión a bases de datos o credenciales mal protegidas).
- **Logs de acceso:** `C:\inetpub\logs\LogFiles` (Útiles en Blue Teaming o Forense).

## 🚀 Check-list de Pentesting:

1. **Enumeración de Versión:** * Usa `nmap -sV` para identificar la versión de IIS. Versiones antiguas como IIS 6.0 o 7.5 tienen vulnerabilidades públicas críticas (ej. Buffer Overflows).
2. **Búsqueda de Directorios:**
    - Usa `Gobuster` o `FFuF`. Busca directorios como `/aspnet_client/` o paneles de administración expuestos.
3. **Extensiones de Archivo:**
    - IIS es nativo para **.ASP** y **.ASPX**. Si encuentras un formulario de subida de archivos, intenta subir una **Webshell en ASPX** para obtener una Reverse Shell.
4. **Configuraciones Erróneas (Misconfigurations):**
    - **Directory Browsing:** Verifica si puedes listar los archivos del servidor simplemente accediendo a una carpeta.
    - **HTTP Methods:** Usa `nmap --script http-methods`. Si el método `PUT` está habilitado, puedes subir tu propio exploit directamente.

>[!tip] **Consejo para el examen:** > Si logras comprometer un servidor IIS, casi siempre estarás operando bajo el usuario `nt authority\network service` o `iis apppool\defaultapppool`. Tu siguiente paso será **Escalada de Privilegios local**.


---
**Navegación:**
- Volver a: [[2.2 Servicios de Windows Frecuentemente Explotados]]
- Volver a: [[3.1 Explotación Vulnerabilidades de Windows]]