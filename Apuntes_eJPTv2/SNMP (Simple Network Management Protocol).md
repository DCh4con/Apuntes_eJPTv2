El **SNMP** es un protocolo de la capa de aplicación utilizado para la gestión y monitorización de dispositivos de red (routers, switches, servidores, impresoras). Es la "puerta trasera de mantenimiento" que, si está mal configurada, expone toda la arquitectura del objetivo.

- **Puerto:** 161 (Consultas) / 162 (Traps).
- **Protocolo:** **UDP**.
- **Riesgo:** Una mala configuración permite listar usuarios, procesos, software instalado y topología de red.

> [!warning] SNMP usa **UDP**, no TCP. Por lo tanto, cuando escanees, ¡acuérdate de usar `-sU` en Nmap!

## 📖 Conceptos Fundamentales
- **Community String:** Es la "contraseña" en texto plano.
    - `public`: Permite acceso de **Solo Lectura** (Read-Only). Muy común.
    - `private`: Permite acceso de **Lectura/Escritura** (Read-Write). Permite tomar control del dispositivo.
- **OID (Object Identifier):** Es un código numérico que identifica un dato específico en el dispositivo (ej. `1.3.6.1.2.1.1.5.0` identifica el hostname).
- **MIB (Management Information Base):** Es el "mapa" o diccionario que traduce los OIDs numéricos a nombres legibles por humanos.

## 🔍 Fase 1: Descubrimiento y Fuerza Bruta
Lo primero es confirmar que el puerto está abierto y tratar de adivinar la Community String.
### 1. Escaneo Inicial (Nmap)
- `nmap -sU -p 161 -sV -sC <IP_OBJETIVO>`
### 2. Fuerza Bruta de Community Strings
Si `public` o `private` no funcionan, usamos el script de Nmap:
- `nmap -sU -p 161 --script snmp-brute <IP_OBJETIVO>`
## 🔬 Fase 2: Enumeración Detallada
Una vez que tenemos una Community String válida (ej. `public`), extraemos toda la información posible.
### Automatización con Nmap (Scripts NSE)
Nmap tiene módulos específicos para agilizar la obtención de datos clave:

```
# Información del sistema (descripción)
nmap -sU -p 161 --script snmp-sysdescr <IP_OBJETIVO>

# Procesos en ejecución
nmap -sU -p 161 --script snmp-processes <IP_OBJETIVO>

# Interfaces de red
nmap -sU -p 161 --script snmp-interfaces <IP_OBJETIVO>

# Software instalado en Windows
nmap -sU -p 161 --script snmp-win32-software <IP_OBJETIVO>
```

### `Snmpwalk`
Es la herramienta estándar para "caminar" por todo el árbol de información del dispositivo.
- `snmpwalk -v 2c -c public <IP_OBJETIVO> > snmp-enum.txt`
	- `-v 2c`: Especifica la versión 2c (la más común).
	- `-c public`: Define la comunidad.
- **¿Qué mirar en ese enorme archivo?** Usa `grep` para buscar cosas interesantes:

```
# Buscar usuarios del sistema
cat snmp-enum.txt | grep -i "user"

# Buscar procesos corriendo
cat snmp-enum.txt | grep -i "process"

# Buscar programas instalados (en Windows)
cat snmp-enum.txt | grep -i "software"

# Buscar nombres de dominio o hostnames
cat snmp-enum.txt | grep -i "hostname"
cat snmp-enum.txt | grep -i "domain"
```

### [[Metasploit]]
Modulo: `auxiliary/scanner/snmp/snmp_enum`
- `set RHOSTS <IP_OBJETIVO>`
- `set COMMUNITY public`
- `run`

## 💥 Fase 3: Explotación y Post-Enumeración
### Escenario A: Acceso "public" (Read-Only)
Aunque no podemos modificar el sistema, la información es vital para:

- **Fuerza Bruta:** Usar los nombres de usuario encontrados para atacar SSH, RDP o SMB.
- **Explotación de Software:** Buscar vulnerabilidades para las versiones exactas de programas detectados por SNMP.
- **Reconocimiento de Red:** Ver tablas de rutas para identificar otras redes internas.

### Escenario B: Acceso "private" (Read-Write)
¡Acceso total! Con permisos de escritura podemos:

1. **Modificar Configuración:** Cambiar rutas de red o deshabilitar servicios mediante `snmpset`.
2. **RCE (Ejecución de Comandos):** En algunos dispositivos (especialmente antiguos o Linux mal configurados), se pueden extender los agentes SNMP para ejecutar scripts del sistema.
3. **Creación de Usuarios:** En routers y switches, se puede intentar inyectar una nueva configuración de usuario administrador.


---
**Navegación:**
- Volver a: [[4.2 Ataques Basados en Red]]