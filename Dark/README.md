# Dark – Vulnyx Writeup

**Autor:** Pxlymxrph  
**Dificultad:** Medium  
**Objetivo:** Obtener acceso root y capturar la bandera.

---

## Descripción

Dark es un reto CTF que nos recuerda la gran importancia de un buen reconocimiento y enumeración del objetivo, pues de ello depende el resultado final de la prueba de penetración

---

## Herramientas Utilizadas

- **Nmap** – Escaneo de puertos y servicios (TCP y UDP)
- **Onesixtyone** – Enumeración SNMP
- **Snmpwalk** – Acceso a información SNMP  

---

## Mitigación y recomendaciones

- Desactivar SNMP si no se usa o migrar a **SNMPv3**.
- Si se permite subir archivos mediante FTP, bloquear ejecución en el directorio (`chmod -x`)
- Filtro de extensiones peligrosas (.php, .phtml, etc).
- Revisar `/etc/sudoers` y elimina entradas genéricas `ALL`.  

---