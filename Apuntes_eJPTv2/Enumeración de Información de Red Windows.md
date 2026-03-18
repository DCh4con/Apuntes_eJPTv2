# 🌐 Post-Explotación: Enumeración de Red en Windows
El objetivo de la enumeración de red es entender la posición de la máquina dentro de la infraestructura. Esto nos permite descubrir nuevas redes, identificar servicios locales ocultos y detectar otros equipos para movernos lateralmente.
## 🎯 1. ¿Qué buscamos?

- **Interfaces:** IP actual y nombre del adaptador de red (Ethernet, Wi-Fi, Túneles).
- **Segmentos de Red:** Identificar si la máquina tiene acceso a redes internas que no vemos desde nuestra posición de ataque.
- **Puertos y Servicios:** ¿Qué puertos están escuchando (`LISTENING`)? Esto revela servicios locales que no son accesibles desde fuera.
- **Vecinos (Hosts):** Dispositivos con los que la víctima ha hablado recientemente (Caché ARP).
- **Rutas:** Cómo viajan los paquetes y hacia dónde apunta la puerta de enlace.
- **Defensas:** ¿Está el Firewall de Windows bloqueando conexiones entrantes o salientes?

## 💻 2. Comandos Esenciales de Windows (CMD)

| **Comando**                              | **Función**                                                                                |
| ---------------------------------------- | ------------------------------------------------------------------------------------------ |
| **`ipconfig`**                           | Muestra IPs, máscaras y puertas de enlace de los adaptadores.                              |
| **`ipconfig /all`**                      | Información detallada: Servidores DNS, DHCP y direcciones MAC.                             |
| **`route print`**                        | Muestra la **Tabla de Enrutamiento**. Crucial para identificar redes internas adicionales. |
| **`arp -a`**                             | Muestra la tabla ARP. Revela IPs de otros equipos en la misma red local.                   |
| **`netstat -ano`**                       | Muestra todas las conexiones y puertos. El flag `-o` muestra el **PID** (ID de proceso).   |
| **`netsh advfirewall show allprofiles`** | Muestra el estado del Firewall (Perfil de Dominio, Privado y Público).                     |
## 🛡️ 3. Enumeración del Firewall
Es vital saber si el firewall está activo antes de intentar subir herramientas o abrir puertos:

- **Ver estado general:**
	- `netsh advfirewall show allprofiles state`
- Ver reglas específicas:
	- `netsh advfirewall firewall show rule name=all`
- Comando antiguo (Windows antiguos/legacy):
	- `netsh firewall show config` 

## ⚡ 4. Tips para el eJPTv2

1. **El Tesoro en Netstat:** Si ves un puerto abierto en `127.0.0.1:3306` (MySQL) pero Nmap no lo detectó desde fuera, significa que el servicio solo acepta conexiones locales. ¡Usa **Port Forwarding** para traértelo a tu Kali!
2. **Identificando el Salto:** Si en `route print` ves una red como `10.1.1.0/24` que no coincide con tu IP actual, has encontrado una red interna. Es el momento de configurar el **Autoroute** en [[Metasploit#🪜 Pivoting|Metasploit]].
3. **PIDs en Netstat:** Usa `netstat -ano` para ver qué proceso (PID) es dueño de un puerto sospechoso. Luego usa `tasklist | findstr <PID>` para saber qué programa es.


---
**Navegación:**
- [[5.1 Post-Explotación Windows]]