# 👥 Enumeración de Activos y OSINT

Busca ampliar la superficie de ataque identificando subdominios, empleados y posibles credenciales filtradas.

### Herramientas de Enumeración
- **Sublist3r**: `sublister -d <dominio> -e <motores>` [Sublist3r-GitHub](https://github.com/aboul3la/Sublist3r)
	- Enumeración pasiva de subdominios.
- **theHarvester**: `theHarvester -d <dominio> -b all` [theHarvester-GitHub](https://github.com/laramies/theHarvester)
	- Recolecta emails, nombres de empleados y hosts de fuentes públicas.

### Fugas y Datos Sensibles
- [HaveIBeenPwned](https://haveibeenpwned.com/): Verificación de brechas en correos corporativos.
- [Wayback Machine](https://web.archive.org/): Recuperación de versiones antiguas de la web para encontrar archivos olvidados.

---
**Navegación:**
- Siguiente fase: [[Análisis Tecnológico y de Contenido]]
- Volver a: [[Reconocimiento Pasivo]]
