Abrir a pasta do usuário de HD em outro PC (link)
```
cmd (como administrador)
takeown /F E: /R
```
    ... habilita a propriedade do drive E:

Verifica portas abertas
```
netstat -a -o -n
```
    .. ou
```
netstat -a -o -n  | findstr 25565
```
