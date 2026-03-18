El secuestro de tokens (Token Impersonation) es una técnica que aprovecha la forma en que Windows gestiona las identidades de los usuarios. Cuando un usuario inicia sesión, el sistema genera **tokens** para que los procesos actúen en su nombre. Si tenemos los privilegios adecuados, podemos suplantar estos tokens para elevar nuestra sesión.
## 🛠️ Requisitos Previos

Para que esta técnica tenga éxito, se deben cumplir las siguientes condiciones:

1. **Sesión de [[Meterpreter]]:** Es necesario tener una sesión activa.
2. **Privilegios Específicos:** El usuario comprometido debe tener el privilegio **`SeImpersonatePrivilege`**.
    - Para verificarlo, ejecuta en Meterpreter: `getprivs`

### 🎭 Metodología Paso a Paso
### 1. Carga y Enumeración
Primero, cargamos la extensión diseñada para gestionar tokens y listamos los que están disponibles en el sistema.

- **Cargar extensión:** `load incognito`
- **Listar tokens disponibles:** `list_tokens -u`

> [!tip] **Delegation vs Impersonation:** > Busca bajo la categoría **"Delegation Tokens"**, ya que son los que permiten iniciar una sesión interactiva completa.

![[Imagenes/ListTokensIncognito.png]]

### 2. Suplantación del Token (Impersonation)
Si identificamos un token de un usuario con mayores privilegios (como `Administrator`), procedemos a suplantarlo.

- **Comando:** `impersonate_token "NOMBRE_DEL_DOMINIO\\USUARIO"`
- **Ejemplo:** `impersonate_token "ATTACKDEFENSE\Administrator"`

![[Imagenes/TokenSuplantadoIncognito.png]]
### 3. Estabilización de la Sesión
Al suplantar un token, nuestra sesión de Meterpreter está "atada" a un hilo de ejecución que podría cerrarse. Para asegurar la persistencia y los privilegios, debemos migrar a un proceso estable del sistema.

1. **Buscar proceso estable (ej. explorer.exe):** `pgrep explorer`
2. **Migrar al proceso:** `migrate <PID_del_explorer>`
### 4. Escalada Final a SYSTEM
Una vez que eres Administrador, puedes repetir el proceso para obtener el nivel máximo de privilegios en la máquina local.

- **Comando:** `impersonate_token "NT AUTHORITY\SYSTEM"`

![[TokenSYSTEMIncognito.png]]
## ⚠️ Notas Técnicas

- **Sesiones Activas:** Solo puedes suplantar tokens de usuarios que tengan o hayan tenido una sesión activa desde el último reinicio.
- **Escenario Común:** Esta técnica es muy efectiva en servidores donde los administradores suelen entrar por RDP para realizar tareas de mantenimiento.

---
**Navegación:**
- Volver a: [[3.2 Escalada de Privilegios en Windows]]]
- [[Técnicas]]