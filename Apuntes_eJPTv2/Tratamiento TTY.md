# 🐚 Tratamiento de la TTY (Shell Interactiva)
Cuando obtenemos una reverse shell, solemos tener una terminal muy limitada. Este proceso "estabiliza" la shell para que funcione como una terminal real. Esto ocurre sobre todo si realizamos la reverse shell con `netcat`.

## 🛠️ Paso 1: Invocar la Bash
Primero, forzamos al sistema a lanzarnos una sesión de bash.:
- `script /dev/null -c bash`
O tambien (para silenciar errores):
- `script /dev/null -c /bin/bash`
## ⚡ Paso 2: Estabilizar el teclado

1. Pulsa **`Ctrl + Z`** (esto enviará tu sesión al segundo plano/background).
2. En tu terminal de Kali, escribe el siguiente comando y pulsa **Enter**:
	- `stty raw -echo; fg`
	(Nota: No verás lo que escribes, o puede que se vea raro, es normal. Al pulsar Enter, volverás a la shell de la víctima).
## 🎨 Paso 3: Configurar el entorno
Ahora que tenemos control de las teclas, reseteamos la apariencia:
- `reset xterm`
- `export TERM=xterm`
- `export SHELL=bash`
## 📐 Ajustar Proporciones (Filas y Columnas)
Si al usar editores como `nano` o `vim` la pantalla se ve pequeña o cortada, debes ajustar el tamaño para que coincida con tu terminal de Kali.

1. **En una nueva pestaña de tu Kali**, mira tus medidas:
	- `stty size`
	- (Ejemplo de salida: `40 120` -> donde 40 son filas y 120 columnas).
2. **En la shell de la víctima**, aplica esas medidas:
	 - `stty rows 40 columns 120`

>[!tip] **¿Por qué hacer esto?** Sin el tratamiento de TTY, si pulsas `Ctrl+C` para detener un proceso, **perderás la conexión por completo**. Con una TTY tratada, puedes usar `Ctrl+C`, el autocompletado con `Tab`, moverte por el historial con las flechas, limpiar la terminal con `Ctrl+L` y usar editores de texto sin errores.


---
**Navegación:**
- Volver a: [[Otros]]