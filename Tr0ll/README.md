# Tr0ll – VulnHub Writeup

**Autor:** Pxlymxrph  
**Dificultad:** Fácil  
**Objetivo:** Obtener acceso root y capturar la bandera.

---

## Descripción

Tr0ll es una máquina vulnerable de VulnHub diseñada para practicar técnicas de penetración en entornos Linux. Ideal practicar técnicas básicas de enumeración y explotación

---

## Herramientas Utilizadas

- **Nmap** – Escaneo de puertos y servicios  
- **Wireshark** – Captura y análisis de paquetes
- **Hydra** – Fuerza bruta de contraseñas  
- **Searchsploit** – Base de datos de vulnerabilidades

---

## Mitigación y recomendaciones

- Mantener kernels actualizados y aplicar parches de seguridad.
- Deshabilitar servicios innecesarios (FTP anónimo).
- Implementar políticas de contraseñas y límites de intentos (fail2ban).
- Monitorizar y auditar logs (auth.log, syslog).

---