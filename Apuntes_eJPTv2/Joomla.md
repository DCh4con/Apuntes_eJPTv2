# 🧩 Pentesting Joomla

**Joomla** es un CMS potente, conocido por su sistema de gestión de usuarios y permisos. En auditorías, suele ser vulnerable a través de extensiones de terceros o malas configuraciones en su panel de administración.

- **Lenguaje:** PHP
- **Base de Datos:** MySQL / PostgreSQL
- **Directorio por defecto:** Suele estar en la raíz `/` o en `/joomla`.

## 🔍 1. Identificación y Reconocimiento

### A. Identificación de Versión y Tecnologías
Al igual que con otros CMS, empezamos confirmando que estamos ante un Joomla y determinando su versión.
- **WhatWeb:**
	- `whatweb http://<IP>/`
- Nmap (Service Detection):
	- `nmap -sV -p80,443 <IP>`

### B. Directorios Interesantes

|**Directorio / Archivo**|**Descripción**|
|---|---|
|`http://<IP>/administrator/`|**Panel de Administración** (Punto clave para el acceso).|
|`http://<IP>/images/`|Directorio común para subir archivos (potencial para Webshells).|
|`http://<IP>/language/en-GB/en-GB.xml`|A veces revela la versión exacta de Joomla en el código XML.|
|`http://<IP>/configuration.php`|Archivo crítico que contiene credenciales de BD (Solo accesible tras compromiso).|

## 🛠️ 2. Enumeración Especializada
### JoomScan (OWASP Joomla Vulnerability Scanner)
Es la herramienta de referencia para Joomla. Te permite detectar versiones, firewalls y vulnerabilidades conocidas.

**Comando básico:**
- `joomscan -u http://<IP>/joomla`

**Resultados clave:** Te indicará si la versión es obsoleta y enumerará los componentes instalados que puedan tener exploits.

![[Imagenes/Joomscan.png]]

Solo queda buscar y configurar los exploits para explotar esas vulnerabilidades.


#
---
**Navegación:**
- Volver a: [[CMS - Content Management System]]