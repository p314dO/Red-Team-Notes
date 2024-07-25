## **LINUX**
Como ya sabemos, los sistemas basados ​​en Linux manejan todo en forma de archivo. En consecuencia, las contraseñas también se almacenan cifradas en un archivo. Este archivo se llama `shadow` archivo y se encuentra en `/etc/shadow`y es parte del sistema de gestión de usuarios de Linux. Además, estas contraseñas se almacenan comúnmente en forma de `hashes`. Un ejemplo puede verse así:

```shell
root@htb:~# cat /etc/shadow  
  
...SNIP...  
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```

htb-estudiante:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7::::
<username>:<encrypted password>:<day of last change>:<min age>:<max age>:<warning period>:<inactivity period>:<expiration date>:<reserved field>
