# 🔎 Searchsploit: Buscador de Exploits Offline
**Searchsploit** es una herramienta de búsqueda para la base de datos **Exploit-DB**. Es extremadamente útil cuando encuentras un servicio y una versión específica (ej. `Apache 2.4.41`) y necesitas ver si existe código público para explotarlo.

## 🛠️ Comandos y Parámetros Principales
| **Parámetro**         | **Función**                                                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------------- |
| **`-h`**              | Muestra el menú de ayuda.                                                                                  |
| **`-u`**              | **Update:** Actualiza la base de datos local (requiere internet). Hazlo antes de un examen.                |
| **`-t <string>`**     | **Title:** Busca el término _solo_ en el título del exploit (reduce el ruido).                             |
| **`-e <string>`**     | **Exact:** Busca la coincidencia exacta del string proporcionado.                                          |
| **`-w`**              | **Web:** Proporciona la URL directa a Exploit-DB para leer detalles online.                                |
| **`-m <id_exploit>`** | **Mirror:** Copia el archivo del exploit a tu carpeta actual.                                              |
| **`-x <id_exploit>`** | **Examine:** Abre el contenido del exploit en la terminal para leerlo antes de copiarlo. (Lo abre con vim) |
## 🚀 Cómo buscar de forma eficiente
### 1. Búsqueda por Servicio y Versión

 - `searchsploit Microsoft Windows SMB`
 - `searchsploit OpenSSH 7.2`
### 2. Búsqueda por CVE
Si ya sabes qué vulnerabilidad tiene el objetivo:

- `searchsploit cve-2021-3156`
### 3. Filtros Combinados
Puedes usar `grep` para filtrar los resultados de Searchsploit por plataforma o tipo:

- Buscar exploits de WordPress que sean en Python
	- `searchsploit wordpress | grep "python"`
- Buscar exploits de privilegios locales en Linux (LPE)
	- `searchsploit linux local | grep "kernel"`

## 📂 Gestión de los archivos de Exploit
Los exploits en Kali se encuentran físicamente en: `/usr/share/exploitdb/exploits/`

> [!danger] **Nunca** debes editarlos ahí. Debes traerlos a tu carpeta de trabajo.
Ej: `searchsploit -m 42315`

>[!warning] **Importante:** Lee siempre los comentarios dentro del exploit. Muchos requieren que instales una librería de Python específica o que cambies variables como la IP (`RHOST`) manualmente dentro del código.


---
**Navegación:**
- Volver a: [[Herramientas]]