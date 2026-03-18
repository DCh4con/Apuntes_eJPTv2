# 📑 Google Dorks

Metodología de uso de operadores avanzados de Google para encontrar información que no debería ser pública.

### Operadores Básicos
- `site:<dominio>`: Filtra resultados solo de ese sitio.
- `filetype:<ext>`: Busca archivos específicos (ex: `filetype:sql`, `filetype:env`).
- `intitle:` / `inurl:`: Busca términos en el título o en la dirección web.
- `cache:`: Muestra la versión almacenada por Google.

### Metodología de Búsqueda avanzada
1. **Exposición de Directorios**: `site:<dominio> intitle:"index of"`
2. **Archivos de Configuración**: `site:<dominio> ext:xml | ext:conf | ext:cnf`
3. **Paneles de Login**: `site:<dominio> inurl:admin | inurl:login`

---

**Navegación:**
- Volver a: [[Reconocimiento Pasivo]]
- [[Técnicas]]