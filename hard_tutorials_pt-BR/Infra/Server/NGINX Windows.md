[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

Install nginx for Windows
```
> cd c:\
> unzip nginx-1.19.6.zip
> cd nginx-1.19.6
> start nginx
```
.. ou
```
> sudo apt update
> sudo apt install nginx
```

Fast shutdown
`> nginx -s stop`

Graceful shutdown
`> nginx -s quit`

Changing configuration, starting new worker processes with a new configuration, graceful shutdown of old worker processes
`> nginx -s reload`

Re-opening log files
`> nginx -s reopen`

Run the tasklist command-line utility to see nginx processes:
`> C:\nginx-1.19.6>tasklist /fi "imagename eq nginx.exe"`

Listagem dos perfis de aplicativo:
```
> ufw app list
> ufw status
```

Ajustando o Firewall
`> sudo ufw allow 'Nginx HTTP'`

Verificando seu Servidor Web
`> systemctl status nginx
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'`

Configurando Blocos do Servidor (recomendado)
`> sudo nano /etc/nginx/sites-available/[example.com](http://example.com)`

Logs do Servidor
```
> /var/log/nginx/access.log
> /var/log/nginx/error.log
```