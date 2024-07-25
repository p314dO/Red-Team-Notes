==Secure Shell==

Secure Shell (`SSH`) es una forma más segura de conectarse a un host remoto para ejecutar comandos del sistema o transferir archivos desde un host a un servidor.El servidor SSH se ejecuta en `TCP port 22`por defecto, al que podremos conectarnos mediante un cliente SSH. Este servicio utiliza tres operaciones/métodos de criptografía diferentes: `symmetric` cifrado, `asymmetric` cifrado, y `hashing`.

#### **Cifrado Simetrico**
El cifrado simetrico utiliza la `same key` para cifrado y descifrado.
Sin embargo, cualquiera que tenga acceso a la clave tambien podra acceder a los datos transmitidos.Por lo tanto, se necesita un procedimiento de intercambio de claves para un cifrado simétrico seguro.
Para ello se utiliza el [método de intercambio de claves Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange).

![[Pasted image 20240722184103.png]]

#### **Cifrado asimetrico**
Usos del cifrado asimétrico **`two SSH keys`**: una clave privada y una clave pública. La clave privada debe permanecer secreta porque solo ella puede descifrar los mensajes que han sido cifrados con la clave pública.
Si un atacante obtiene la clave privada, que a menudo no está protegida con contraseña, podrá iniciar sesión en el sistema sin credenciales.
Una vez que se establece una conexión, el servidor utiliza la clave pública para la inicialización y autenticación. Si el cliente puede descifrar el mensaje, tiene la clave privada y la sesión SSH puede comenzar. El método hash convierte los datos transmitidos en otro valor único. SSH utiliza hash para confirmar la autenticidad de los mensajes. Este es un algoritmo matemático que solo funciona en una dirección.

## FUERZA BRUTA
#### Hydra
```ruby
hydra -L user.list -P password.list ssh://10.129.42.197
```

## INTEARACTUAR

#### SSH
```ruby
ssh user@IP
```

