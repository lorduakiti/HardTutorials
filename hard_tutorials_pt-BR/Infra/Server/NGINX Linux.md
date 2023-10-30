Instalando
`> apt install nginx`
    .. ou
```
> apt-get install nginx
> service nginx start

> systemctl status nginx
> systemctl start nginx
> systemctl restart nginx
> systemctl reload nginx
> systemctl stop nginx
> systemctl disable nginx
> systemctl enable nginx
```

`> ps | grep nginx`
    .. ou
`> systemctl status nginx`

Configurando
`> cd /etc/nginx/sites-available`
.. ou
```
> mkdir /etc/nginx/sites-available
> mkdir /etc/nginx/sites-enabled

> nano /etc/nginx/nginx.conf
	bloco http {
		include /etc/nginx/sites-enabled/*;
	}
```

```
> cd /etc/nginx/sites-available
> nano nome_projeto
	bloco http {
		server {
			server_name meudominio.com.br www.meudominio.com.br;
		}
		location / {
			proxy_pass http://127.0.0.1:8000;
			proxy_set_header X-Forwarded-Host $server_name;
			proxy_set_header X-Real-IP $remote_addr;
			add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
		}
	}

> cd /etc/nginx/sites-enabled
> ln -s /etc/nginx/sites-available/agenda
> rm default

> service nginx restart
```

Configuração de redirecionamento de sites
```
nano /etc/nginx/sites-enabled/nome_site.conf
server {
	listen 80;
	listen [::]:80;
	index index.html index.htm;
	server_name nome_site.com.br;
	location / {
		proxy_set_header Host $host;
		# .. ou
		# proxy_set_header Host novo_nome_site.com.br;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
		proxy_pass http://www.novo_nome_site.com.br;
	}
}
```

`> service nginx reload`
    .. ou
`> service nginx restart`

Configuração de sites estáticos
```
nano /etc/nginx/sites-available/nome_projeto
bloco http {
	...
	location /static/ {
		alias /var/www/nome_projeto/static/;
	}
}
```
.. ou
```
nano /etc/nginx/sites-enabled/default
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	root /var/www/html;
	index index.html index.htm index.ngnix-debian.html;
	server_name _;
	location / {
		try_files $uri $uri/ =404;
	}
}
```
.. ou
```
nano /etc/nginx/sites-enabled/nome_site.conf
server {
	listen 80;
	listen [::]:80;
	root /var/www/nome_site;
	index index.html index.htm;
	server_name nome_site.com.br;
	location / {
		try_files $uri $uri/ =404;
	}
}
```

`> service nginx reload`
    .. ou
`> service nginx restart`

Verificano log
`> tail -f /var/log/ngnix/error.log`

Alterando redirecionamento de rotas
-- p/ Linux
```
> nano /etc/hosts
> 127.0.0.1 nome_site.com.br
> ping nome_site.com.br
```