## 👥 5. Enumeración de Usuarios y Grupos

El objetivo es mapear el ecosistema de cuentas en la máquina víctima para identificar objetivos con mayores privilegios o sesiones activas que podamos explotar.

### 🎯 ¿Qué buscamos?

- **Identidad:** ¿Quién soy y qué permisos tengo exactamente?
- **Sesiones:** ¿Hay otros usuarios logueados en este momento?
- **Administradores:** ¿Quiénes pertenecen al grupo de Administradores?
- **Configuración:** Detalles de cuentas (contraseñas que no expiran, descripciones, etc.).

### 💻 Comandos en CMD (Windows Nativo)
Estos comandos son esenciales cuando tienes una shell básica y necesitas moverte rápido:

|**Comando**|**Función**|
|---|---|
|`whoami`|Muestra el usuario actual.|
|`whoami /priv`|**Vital:** Muestra los privilegios del token actual (ej. `SeImpersonatePrivilege`).|
|`query user`|Muestra las sesiones de usuarios activas en el sistema.|
|`net users`|Lista todos los usuarios locales creados en la máquina.|
|`net user <usuario>`|Información detallada de un usuario (ej. pertenencia a grupos).|
|`net localgroup`|Lista todos los grupos locales del sistema.|
|`net localgroup Administrators`|Lista específicamente a los usuarios con **privilegios de administrador**.|

### ⚡ Comandos en Meterpreter
Si tienes una sesión de Meterpreter, puedes obtener esta información de forma más directa o mediante módulos automatizados:

- **`getuid`**: Obtiene el nombre del usuario bajo el cual corre la sesión.
- **`getprivs`**: Muestra los privilegios que tiene el usuario actual en la sesión activa.
- **Módulo `post/windows/gather/enum_logged_on_users`**: Ideal para ver quién ha estado trabajando en la máquina recientemente.
	 - `set SESSION <id_sesion>`
### 💡 Tips para el eJPTv2

- **Privilegios Críticos:** Si al usar `whoami /priv` o `getprivs` ves **SeImpersonatePrivilege**, estás ante una oportunidad de oro para escalar a SYSTEM usando técnica de [[Suplantar Token de Acceso]]
- **Descripciones en Net User:** A veces los administradores anotan contraseñas temporales o propósitos de la cuenta en el campo de "Comment" del comando `net user <usuario>`.
- **Grupos de Interés:** Además de _Administrators_, busca grupos como _Remote Desktop Users_ o _Remote Management Users_, ya que te indican qué usuarios pueden entrar por RDP o WinRM.

- ---
**Navegación:**
- [[5.1 Post-Explotación Windows]]