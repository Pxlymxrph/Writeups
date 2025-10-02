# IMF — Writeup (Resumen)
**Autor:** Pxlymxrph  
**URL:** https://www.vulnhub.com/entry/imf-1,162/  
**Dificultad:** Easy / Medium  
**Etiquetas:** ctf, vulnhub, linux, ssh, privesc, bof, enum

---

## TL;DR
Se identificó y explotó una vulnerabilidad encadenada en la VM **IMF (VulnHub)** que incluyó: enumeración web, bypass de autenticación mediante *type juggling*, uso de SQLi para localizar recursos ocultos, subida controlada de archivos en un panel, y análisis estático/dinámico de un binario local (`agent`) que contenía un **buffer overflow** en la opción 3. Este repositorio contiene un resumen público y evidencia segura; los detalles técnicos explotables se mantienen fuera de este paquete por responsabilidad educativa.

---

## Descripción breve de la resolución
1. **Descubrimiento de red:** localicé la IP de la VM en la LAN (ej. `192.168.1.74`).  
2. **Enumeración de servicios:** `nmap` mostró servicio HTTP. Se inspeccionó la web y se hallaron cadenas codificadas en base64.  
3. **Acceso web:** se descubrió un panel administrativo oculto; mediante una técnica de *type juggling* fue posible autenticarse como un usuario privilegiado.  
4. **SQLi & archivos ocultos:** uso de `sqlmap` permitió enumerar recursos y encontrar una imagen con un QR que condujo a un nuevo directorio.  
5. **File upload:** se aprovechó el panel de subida (en el contexto de la VM) para transferir un archivo útil para la interacción local.  
6. **Local / binario:** se localizó el binario `agent`, se recuperó el ID requerido, y mediante análisis se identificó una ruta vulnerable (opción 3) susceptible a buffer overflow.  

---

## 🛠️ Herramientas Utilizadas
- **Nmap** – Escaneo de puertos y servicios  
- **Gobuster** – Fuerza bruta de directorios  
- **Sqlmap** – Ataques de inyección sql  
- **BurpSuite** – Interceptar peticiones web   
- **Ghidra** – Ingeniería inversa


---

## Recomendaciones resumidas (para desarrolladores)
- Evitar funciones inseguras (p. ej. `gets`) y validar siempre la longitud de las entradas.  
- Compilar con protecciones modernas: `-fstack-protector-strong`, `-D_FORTIFY_SOURCE=2`, RELRO completo, PIE, NX.  
- Incluir sanitizers y fuzzing en el pipeline de pruebas para detectar regresiones de memoria.

---