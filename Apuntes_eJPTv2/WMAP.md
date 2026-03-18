**WMAP** es un plugin de Metasploit diseñado para automatizar el escaneo de aplicaciones web. Su potencia reside en que **orquesta los módulos auxiliares** (`auxiliary/scanner/http/...`) para lanzarlos de forma masiva contra un objetivo.

> [!note] **Integración Total:** Todos los hallazgos se guardan automáticamente en la base de datos de Metasploit (`msfdb`), permitiendo consultar los resultados con comandos como `vulns` o `services`.

## 🛠️ Metodología Paso a Paso

### Fase 0: Inicialización del Entorno
Antes de abrir Metasploit, debemos asegurar que la base de datos (PostgreSQL) esté activa para no perder los resultados del escaneo.

1. **Iniciar base de datos:** `sudo service postgresql start`
2. **Verificar estado:** `msfdb status` (o `msfdb init` si es la primera vez).
### Fase 1: Carga del Plugin
Dentro de `msfconsole`:

- **Comando:** `load wmap`
- **Verificación:** Al cargarse correctamente, aparecerán nuevos comandos que empiezan por `wmap_`.

> [!note] Puedes ver los nuevos comando cargados de WMAP usando `help` o escribiendo `wmap_` y pulsando TAB para ver las opciones
### 🎯Fase 2: Gestión de Sitios (Sites)
Los "sites" son los dominios o IPs que vamos a auditar.

| **Paso**          | **Comando**                        | **Descripción**                                                                |
| ----------------- | ---------------------------------- | ------------------------------------------------------------------------------ |
| **Info**          | `wmap_sites -h`                    | Muestra las opciones del comando `wmap_sites`                                  |
| **Añadir Sitio**  | `wmap_sites -a http://<TARGET_IP>` | Registra la raíz del servidor en la base de datos.                             |
| **Listar Sitios** | `wmap_sites -l`                    | Muestra los sitios registrados y su **ID** (necesario para el siguiente paso). |

### 🎯Fase 3: Definir Objetivos (Targets)
Aquí especificamos qué ruta exacta del sitio vamos a escanear.

| **Paso**                      | **Comando**                                           | **Descripción**                                                       |
| ----------------------------- | ----------------------------------------------------- | --------------------------------------------------------------------- |
| **Info**                      | `wmap_targets -h`                                     | Muestra las opciones del comando `wmap_targets`                       |
| **Definir Target (Opción A)** | `wmap_targets -d <ID>`                                | Selecciona el sitio (por ID) para ser el objetivo activo del escaneo. |
| **Definir Target (Opción B)** | `wmap_targets -t http://<TARGET_IP>/ruta/ejemplo.php` | Añadir directamente la URL completa con ruta específica               |
| **Listar Targets**            | `wmap_targets -l`                                     | Verifica que el objetivo está correctamente cargado.                  |

### ⚡Fase 4: Escaneo - Listar Módulos (Dry Run)
Aquí es donde WMAP decide qué módulos lanzar basándose en la información que detecta (puerto, SSL, tipo de servidor).
#### El "Dry Run" (Verificación)

- **Comando:** `wmap_run -t`
- **Función:** Muestra la lista de módulos habilitados para el target. **No lanza ataques**, solo sirve para comprobar qué se va a testear (versión de servidor, directorios, archivos `.file`, etc.).

### ⚡Fase 5: Ejecutar el Escaneo (El Ataque)
Este es el comando que realmente lanza los módulos contra el objetivo.

- **Comando:** `wmap_run -e`
- **Función:** Lanza todos los módulos seleccionados.

> [!tip] Este proceso es ruidoso y puede tardar. Verás en tiempo real cómo Metasploit prueba vulnerabilidades como _Directory Traversal_, _Inyección SQL_ o _XSS_.

### 📊Fase 6: Visualizar Resultados
Una vez finalizado el escaneo (o incluso mientras se ejecuta), podemos consultar la base de datos.

- **Hallazgos de WMAP:** `wmap_vulns -l` (Muestra los resultados específicos del plugin).
- **Hallazgos Globales:** `vulns` (Muestra todas las vulnerabilidades registradas en el workspace actual de Metasploit).

## 🚩 Notas para el Examen eJPTv2

- **No es infalible:** WMAP es excelente para encontrar archivos "olvidados" y malas configuraciones de servidor, pero puede dar falsos negativos en lógica de aplicación compleja.
- **Complemento:** Siempre úsalo junto a herramientas como **Nikto** o **Gobuster**.
- **Workspaces:** Recuerda siempre crear un `workspace -a <nombre>` en Metasploit antes de empezar para mantener los resultados de WMAP organizados por proyecto.

---
**Navegación:**
- Volver a: [[2.4 Escáner de Vulnerabilidades Automático]]