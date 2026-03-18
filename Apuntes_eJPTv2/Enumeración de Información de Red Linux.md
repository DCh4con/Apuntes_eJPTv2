# 🌐 Enumeración de Red en Linux
El objetivo es mapear las conexiones de la máquina víctima. Buscamos identificar otras redes a las que el host tenga acceso y servicios que solo escuchan de forma local (`localhost`).

## 🎯 1. ¿Qué buscamos?

- **Interfaces:** IP actual, máscaras de red y nombres de adaptadores (eth0, ens33, lo).
- **Segmentos Ocultos:** ¿Tiene la máquina una segunda tarjeta de red conectada a una VLAN interna?
- **Servicios Locales:** Puertos abiertos que no son visibles desde el exterior (ej. una base de datos escuchando solo en `127.0.0.1`).
- **Vecinos:** Otros equipos con los que la víctima se ha comunicado (Caché ARP).
- **Resolución de Nombres:** Cómo traduce la máquina los nombres de dominio a IPs (DNS).

## 💻 2. Comandos de Enumeración (Nativos)

### 🔌 Interfaces y Direccionamiento

|**Comando**|**Función**|
|---|---|
|`ip a`|**El estándar moderno:** Muestra todas las interfaces, IPs y estado.|
|`ifconfig`|Comando clásico para ver IPs (puede no estar instalado en sistemas nuevos).|
|`ip route`|Muestra la **Tabla de Enrutamiento** y la puerta de enlace predeterminada.|
|`arp -a`|Muestra la tabla ARP para identificar otros hosts en el mismo segmento.|
### 🔍 Servicios y Puertos

|**Comando**|**Función**|
|---|---|
|`netstat -tunlp`|Muestra servicios TCP/UDP, puertos y el PID del proceso que los usa.|
|`ss -tunlp`|Versión moderna de netstat, más rápida y detallada.|

### 📛 Configuración DNS y Dominios
En Linux, la resolución de nombres depende de varios archivos críticos:

- **`cat /etc/hosts`**: Muestra mapeos estáticos de IP a Nombre (útil para encontrar nombres de servidores internos).
- **`cat /etc/resolv.conf`**: Muestra las direcciones de los servidores DNS que usa el sistema.
- **`cat /etc/nsswitch.conf`**: Indica el orden en que el sistema busca los nombres (si mira primero en `hosts` o en `DNS`).

## ⚡ 3. Enumeración vía Meterpreter
Si tienes una sesión de Meterpreter en Linux, puedes obtener esta info sin abrir una shell:

- **`ifconfig`**: Lista interfaces e IPs de forma clara.
- **`netstat`**: Lista conexiones activas y puertos en escucha.
- **`arp`**: Muestra la tabla de vecinos descubiertos.
- **`route`**: Muestra la tabla de enrutamiento (vital para configurar el `autoroute`).

## 💡 4. Tips para el eJPTv2

1. **Localhost (127.0.0.1):** Si ves un puerto como el `3306` (MySQL) o `8080` en `netstat` que no aparecía en tu escaneo de Nmap, usa **[[Metasploit#🚪 Método 2 Port Forwarding (Túnel específico)|Port Forwarding]]** de Metasploit para acceder a él.
2. **IPs Inusuales:** Si `ip a` muestra una interfaz con una IP tipo `10.x.x.x` y otra `172.x.x.x`, estás ante un punto de pivote.
3. **Análisis de /etc/hosts:** A veces los administradores ponen IPs de máquinas de desarrollo o bases de datos con sus nombres aquí. Es una mina de oro para identificar objetivos.


---
**Navegación:**
- Volver a: [[5.2 Post-Explotación Linux]]