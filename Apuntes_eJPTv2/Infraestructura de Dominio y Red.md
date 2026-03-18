# 🌐 Infraestructura de Dominio y Red

Fase dedicada a identificar dónde vive el objetivo y cómo se comunica.

### Herramientas CLI
- **host**: `host <dominio>` (Resolución básica de IP).
- **dnsrecon**: Herramienta integral para DNS.
	- `dnsrecon -d <dominio>` (Enumeración estándar y AXFR).
	- Permite: Verificación de DNSSEC, registros PTR y ataques de caché.
- **Whois**: `whois <dominio>` (Datos de registro).

### Recursos Online
- [WHO.is](https://who.is/) / [Netcraft](https://sitereport.netcraft.com/): Información de registro y hosting.
- [DNSDumpster](https://dnsdumpster.com/): Visualización gráfica de registros DNS.

---
**Navegación:**
- Siguiente fase: [[Enumeración de Activos y OSINT]]
- Volver a: [[Reconocimiento Pasivo]]
