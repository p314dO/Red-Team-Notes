Domain Name System

## ENUMERATION

#### Banner Grabbing
```ruby
dig version.bind CHAOS TXT @IP
```

#### Reverse Lookup
```js
dig -x DOMINIO_IP @IP
```

#### Any Record
```ruby
dig any victim.com @IP
```
#### Zone Transfer
```
dig axfr domain.com @IP
```


