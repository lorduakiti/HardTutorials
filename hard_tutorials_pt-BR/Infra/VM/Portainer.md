```
docker pull portainer/portainer
mkdir /home/docker
docker run -d -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /home/docker/portainer/data:/data portainer/portainer

docker start portainer
```