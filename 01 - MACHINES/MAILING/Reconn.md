Ejecuto mi script de recon Esdras.
```
start.py Mailing 10.10.11.14
```

![[Pasted image 20240722201823.png]]

Este script me genera todos los directorios de trabajo necesarios, luego ejecuta un primer escaneo con nmap para detectar los puertos abiertos en la maquina victima y despues otro mas para reconocer los servicios que se ejecutan en esos puertos abiertos.

![[Pasted image 20240722203116.png]]

file=../../../Program+Files+(x86)/hMailServer/bin/hMailServer.INI

![[Pasted image 20240722203237.png]]

AdministratorPassword=841bb5acfa6779ae432fd7a4e6600ba7
Password=0a9f8ad8bf896b501dde74f08efd7e4c

841bb5acfa6779ae432fd7a4e6600ba7:
homenetworkingadministrator
