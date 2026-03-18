# 🛠️ Nmap (Network Mapper)

Nmap es la herramienta estándar para el descubrimiento de redes y auditoría de seguridad.

## 🔍 1. Descubrimiento de Hosts y Puertos
| **Parámetro**       | **Función**           | **Notas adicionales**                                                              |
| ------------------- | --------------------- | ---------------------------------------------------------------------------------- |
| nmap`<IP>`          | Escaneo estándar      | Escanea los 1000 puertos TCP más comunes.                                          |
| `-p-`               | Rango completo        | Escanea los 65535 puertos.                                                         |
| `-p <ports>`        | Escaneo personalizado | Escanea los puertos seleccionados.                                                 |
| `-F`                | Fast scan             | Escanea solo los 100 puertos más frecuentes.                                       |
| `-sU`               | Escaneo **UDP**       | Protocolo UDP (más lento que TCP).                                                 |
| `-Pn`               | No Ping               | Asume que el host está vivo. Útil contra el firewall de Windows o si bloquean ICMP |
| `-n`                | No DNS                | No realiza resolución de nombres (más rápido).                                     |
| `--open`            | Solo abiertos         | Filtra y muestra únicamente puertos con estado `open`.                             |
| `-sn <red/máscara>` | Escaner de hosts      | No escanea puertos, solo verifica actividad                                        |
## 🛡️ 2. Análisis de Servicios y SO

| **Parámetro** | **Función**       | **Descripción**                                                   |
| ------------- | ----------------- | ----------------------------------------------------------------- |
| `-sV`         | Versión           | Detecta versiones de servicios (`--version-intensity 0-9`).       |
| `-O`          | Sistema Operativo | Intenta identificar el SO (`--osscan-guess` para más precisión).  |
| `-sC`         | Scripts (NSE)     | Lanza conjunto de scripts populares para buscar vulnerabilidades. |
| `-A`          | Modo Agresivo     | Combinación de: `-sV`, `-sC`, `-O` y `--traceroute`.              |
**Ubicación de Scripts:** `/usr/share/nmap/scripts/`
## ⚙️ 3. Tipos de Escaneo (Técnicas)

| **Tipo**        | **Flag** | **Mecanismo**         | **Comportamiento / Uso**                                                      |
| --------------- | -------- | --------------------- | ----------------------------------------------------------------------------- |
| **TCP Connect** | `-sT`    | `SYN → SYN/ACK → ACK` | Por defecto si se usa nmap como usuario sin privilegios (ruidoso).            |
| **SYN Stealth** | `-sS`    | `SYN → SYN/ACK → RST` | Por defecto si se usa nmap como **root**. No completa la conexión (sigiloso). |
| **NULL**        | `-sN`    | Sin Flags             | Si no hay respuesta: Abierto/Filtrado. Si RST: Cerrado.                       |
| **FIN**         | `-sF`    | Flag FIN              | Si no hay respuesta: Abierto/Filtrado. Si RST: Cerrado.                       |
| **Xmas**        | `-sX`    | FIN, PSH, URG         | Si no hay respuesta: Abierto/Filtrado. Si RST: Cerrado.                       |
| **ACK Scan**    | `-sA`    | Flag ACK              | Mapea firewalls. RST = No filtrado. Sin respuesta = Filtrado.                 |
> [!caution] **Importante:** `-sN, -sF y -sX`, no funcionan en Windows modernos, ya que independientemente de como esté el puerto (abierto, filtrado o cerrado) siempre responde con un RST a estos 3 tipos de escaneos

## 🚀 4. Optimización y Rendimiento

Plantillas de temporizado:

| **Parámetro** | **Nombre** | **Uso recomendado**                                            |
| ------------- | ---------- | -------------------------------------------------------------- |
| **-T0**       | Paranoid   | Paranoico, evadir IDS, extremadamente lento.                   |
| **-T1**       | Sneaky     | Furtivo, para redes muy sensibles.                             |
| **-T2**       | Polite     | Educado, consume poco ancho de banda.                          |
| **-T3**       | Normal     | **Por defecto**. Equilibrio velocidad/ruido.                   |
| **-T4**       | Aggressive | Recomendado en redes locales o auditorías con tiempo limitado. |
| **-T5**       | Insane     | Muy rápido, puede perder paquetes o saturar la red.            |
**Otras opciones de optimización:**
- `--min-rate <val>`: Mantiene un envío mínimo de paquetes por segundo.
- `--host-timeout <val>`: Tiempo límite para no quedar atascado en un host lento.
	- 5s → s para segundos
	- 5m → m para minutos
	- 5h → h para horas
## 🥷 5. Evasión de Firewalls e IDS

| **Parámetro**                 | **Función**                | **Técnica**                                                                                                                                                  |
| ----------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-f` / `-ff`                  | Fragmentación              | Divide paquetes en 8 / 16 bytes para saltar firmas.                                                                                                          |
| `--mtu <val>`                 | MTU específica             | Fragmentación personalizada (múltiplos de 8).                                                                                                                |
| `--scan-delay <ms>`           | Retraso                    | Pausa fija entre paquetes para no levantar alertas.                                                                                                          |
| `-D <IP1,IP2...>`             | Decoys                     | Envía señuelos para camuflar tu IP entre otras.                                                                                                              |
| `-g o --source-port <puerto>` | Source Port                | Suplanta el puerto de origen (ej: 53, 80, 443 para evadir filtros).                                                                                          |
| `--data-length <val>`         | Tamaño extra               | Añade datos aleatorios a los paquetes para cambiar su tamaño.                                                                                                |
| `-ttl <val>`                  | Time To Live (TTL) inicial | Establece el valor TTL inicial de los paquetes. Sirve para saltar firewalls que filtran por TTL o para emular otros sistemas operativos (OS fingerprinting). |

## 📑 6. Formatos de Salida (Output)

| **Parámetro** | **Formato** | **Utilidad**                                                   |
| ------------- | ----------- | -------------------------------------------------------------- |
| `-oN <file>`  | Normal      | Formato normal. Salida igual que por terminal.                 |
| `-oX <file>`  | XML         | Importar en otras herramientas, como [[Metasploit]].           |
| `-oG <file>`  | Grepable    | Ideal para usar con `grep`, `awk` o `cut` (REGular EXpresion). |

---

**Navegación:**
- Volver a: [[Reconocimiento Activo]]
- [[Herramientas]]