### Container
```
docker pull dremio/dremio-oss
docker run -p 9047:9047 -p 31010:31010 -p 45678:45678 dremio/dremio-oss
```

    .. ou

```
mkdir -p $HOME/docker/volumes/dremio

docker run -d --name dremio-docker -p 9047:9047 -p 31010:31010 -p 45678:45678 -v $HOME/docker/volumes/dremio:/var/lib/dremio/data  dremio/dremio-oss

ps aux | grep dremio

docker restart  dremio-docker
```

erro windows
```
Net stop com.docker.service
Net start com.docker.service

cd "C:\Program Files\Docker\Docker"
./DockerCli.exe -SwitchDaemon
```

Linux WSL
-- p/ ubuntu
```
sudo apt-get install alien

wget -o downloaded_dremio_rpm_file.rpm  [https://download.dremio.com/community-server/1.0.8-201707190805180330-27f36e1/dremio-community-1.0.8-201707190805180330_27f36e1_1.noarch.rpm](https://download.dremio.com/community-server/1.0.8-201707190805180330-27f36e1/dremio-community-1.0.8-201707190805180330_27f36e1_1.noarch.rpm)

sudo alien –scripts -d downloaded_dremio_rpm_file.rpm
sudo chmod 0777 dremio_file.deb
sudo dpkg –install dremio_file.deb
sudo service dremio status
sudo service dremio start

[http://localhost:9047/](http://localhost:9047/)
```

  

-- p/ ubuntu 17
### First, install Java 1.8+, 64 bit.
```
sudo apt-get update
sudo apt-get install default-jre
#Install script
sudo groupadd -r dremio
sudo useradd -r -g dremio -d /var/lib/dremio -s /sbin/nologin dremio
sudo mkdir /opt/dremio
sudo mkdir /opt/dremio/log && sudo chown dremio:dremio /opt/dremio/log
sudo mkdir /opt/dremio/run && sudo chown dremio:dremio /opt/dremio/run
sudo mkdir /opt/dremio/data && sudo chown dremio:dremio /opt/dremio/data
```
### Latest version link can be obtained on [https://www.dremio.com/download/](https://www.dremio.com/download/) under 'TAR'
```
cd /tmp

wget -o downloaded_dremio_tar_file.tar.gz  [https://download.dremio.com/community-server/1.0.8-201707190805180330-27f36e1/dremio-community-1.0.8-201707190805180330-27f36e1.tar.gz](https://download.dremio.com/community-server/1.0.8-201707190805180330-27f36e1/dremio-community-1.0.8-201707190805180330-27f36e1.tar.gz)

sudo tar -xvf downloaded_dremio_tar_file.tar.gz -C /opt/dremio/ --strip-components=1
sudo tar -xvf dremio-community-1.0.8-201707190805180330-27f36e1.tar.gz -C /opt/dremio/ --strip-components=1
sudo ln -s /opt/dremio/conf /etc/dremio
sudo cp /opt/dremio/share/dremio.service /etc/systemd/system/dremio.service
```

### Add service
```
sudo systemctl daemon-reload
sudo systemctl start dremio
```

### Optionally, set Dremio service to start on boot:
`sudo systemctl enable dremio`