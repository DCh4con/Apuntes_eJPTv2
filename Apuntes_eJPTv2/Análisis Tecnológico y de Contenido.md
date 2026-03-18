# 🛠️ Análisis Tecnológico y de Contenido

Identificación del stack tecnológico y preparación de servidores.

### Fingerprinting (Tecnologías)
- **Wappalyzer / BuiltWith**: Extensiones de navegador para detectar CMS, frameworks y lenguajes.
- **wafw00f**: `wafw00f <dominio>` (Identifica si hay un WAF/cortafuegos activo). [wafw00f-GitHub](https://github.com/EnableSecurity/wafw00f)

### Verificaciones Manuales Críticas
- **robots.txt**: Ubicado en `dominio/robots.txt`. Indica rutas ocultas para crawlers.
- **sitemap.xml**: Ubicado en `dominio/sitemap.xml`. Mapa de la estructura web.

### Análisis en Local
- **httrack**: `httrack <url>` (Descarga la web para un análisis offline sin dejar rastro).

---
**Navegación:**
- Volver a: [[Reconocimiento Pasivo]]