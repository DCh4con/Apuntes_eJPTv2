# 📂 SMB / SAMBA - Apuntes eJPTv2
**Samba** es la implementación libre del protocolo **SMB (Server Message Block)** que permite a sistemas Linux compartir archivos e impresoras, interactuando perfectamente con redes Windows. 

## 🌐 Puertos de Funcionamiento

- **Puerto 445 (TCP):** SMB moderno que corre directamente sobre TCP/IP. Es el estándar actual.
- **Puerto 139 (TCP):** SMB antiguo que corre sobre NetBIOS. Lo verás en sistemas heredados (Legacy).

## 🔍 Conceptos Críticos para el Pentester:

1. **Null Sessions (Sesiones Nulas):**
    - Es una configuración debil donde el servidor permite a un atacante conectarse **sin usuario ni contraseña** para listar usuarios, grupos y recursos compartidos. Es el "santo grial" de la enumeración inicial.
2. **Recursos Compartidos (Shares):**
    - Son las carpetas que el servidor ofrece. Siempre debes buscar el recurso `IPC$` (usado para comunicación entre procesos) y recursos comunes como `\backup`, `\public` o `\users`.
3. **SambaCry / EternalBlue:**
    - Aunque **EternalBlue (MS17-010)** es de Windows, Samba tuvo su propia versión llamada **SambaCry**. Ambos permiten la ejecución remota de comandos (RCE) explotando fallos en el manejo de paquetes SMB.

## 🚀 Metodología de Ataque (eJPTv2):

- **Paso 1: Enumeración Exhaustiva**
    - Tu mejor amigo es `enum4linux`. Ejecuta: `enum4linux -a <IP>` para intentar extraer usuarios, políticas de contraseñas y recursos de un solo golpe.
- **Paso 2: Listar Recursos sin Contraseña**
    - Usa `smbclient`: `smbclient -L //<IP>/ -N` (la `-N` es para "No Password"). Si ves una lista de carpetas, intenta entrar en ellas.
- **Paso 3: Conexión al Recurso**
    - Si encuentras una carpeta llamada `docs`, conéctate: `smbclient //<IP>/docs -N`. Una vez dentro, usa `ls`, `cd` y `get` para descargar archivos sensibles.
- **Paso 4: Fuerza Bruta y Spraying**
    - Si tienes una lista de usuarios pero no contraseñas, usa [[Crackmapexec]] (CME). Es ultra rápido para SMB: `crackmapexec smb <IP> -u usuarios.txt -p passwords.txt`

> [!caution] **Nota:** En el examen, si encuentras un usuario y contraseña válidos para SMB, ¡pruébalos también en SSH o RDP! Los usuarios suelen reciclar la misma contraseña para todos los servicios del servidor.


---
**Navegación:**
- Volver a: [[2.3 Servicios de Linux Frecuentemente Explotados]]
- Volver a: [[3.4 Servicios de Linux Frecuentemente Explotados]]