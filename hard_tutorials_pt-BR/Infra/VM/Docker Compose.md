-- p/ windows
https://docs.docker.com/docker-for-windows/install/
https://github.com/docker/compose/releases
   atualizando..
```
docker-compose  migrate-to-labels
```
   ou..
```
docker container  rm -f -v myapp_web_1 myapp_db_1 ...
```

-- p/ linux
```
curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker-compose  --version
```
   .. atualizando
```
docker-compose  migrate-to-labels
```

--

```
docker-compose  up
```
