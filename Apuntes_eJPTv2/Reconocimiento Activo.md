# ⚡ Reconocimiento Activo

A diferencia del [[Reconocimiento Pasivo]], aquí interactuamos directamente con el objetivo. Existe un **riesgo de detección** real, ya que nuestras peticiones quedarán registradas en los logs del sistema o pueden activar alertas en un IDS/IPS.

## 1. Infraestructura DNS (Transferencia de Zona)
El objetivo es obtener una copia completa del archivo de zona de un servidor DNS para listar todos los hosts internos.
- **dnsenum**: `dnsenum <dominio>`
- **dig (AXFR)**: `dig axfr @<servidor_dns> <dominio>`

> [!tip] **Recomendación**: Primero identifica los servidores con `dnsrecon -d <dominio>` para saber a qué servidor lanzar el `dig`. Serán los servidores dns SOA y NS

## 2. Descubrimiento de Hosts (Host Discovery)
Identificar qué máquinas están activas en una red antes de escanear puertos.
- **arp-scan**: `sudo arp-scan -I <interfaz> --localnet` (Solo para red local/Capa 2).
- **Nmap (Ping Sweep)**: `sudo nmap -sn <red/prefijo>` (No escanea puertos, solo verifica actividad).

## 3. Escaneo de Puertos y Servicios
Una vez identificados los hosts vivos, procedemos a identificar los servicios abiertos.
- **Herramienta principal:** [[Nmap]]
- **Alternativas:** [[Metasploit#🔍 Módulos de Escáner de Puertos|Metasploit]] (Módulos de escaneo de puertos y enumeración).

---
**Navegación:**
- Volver a: [[1.1 Reconocimiento Pasivo vs Activo#⚡ Reconocimiento Activo|1.1 Reconocimiento Pasivo vs Activo]]
- Herramienta detallada: [[Nmap]]