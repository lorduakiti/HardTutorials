Instalação
```
apt-get remove docker docker-engine docker.io containerd runc
```

```
apt-get update
apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
apt-key fingerprint 0EBFCD88
add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
    .. ou
```
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
apt-key fingerprint 0EBFCD88
add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
```

```
apt-get update

apt-get install docker-ce docker-ce-cli containerd.io
```
    .. ou
`apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io`
    .. ou
`apt-get install docker`

-- p/ WSL
```
apt-get install curl -y
curl -sSL https://get.docker.com/ | sh
service docker stop
usermod -aG docker
usermod -aG docker $USER
nohup docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &
docker info
```

-- p/ windows
```
& $Env:ProgramFiles\Docker\Docker\DockerCli.exe -SwitchDaemon .
```

  

```
cd "C:\Program Files\Docker\Docker"
./DockerCli.exe -SwitchDaemon
```

Restart-Service docker

-- teste p/ windows
```
docker pull mcr.microsoft.com/windows/nanoserver:1903
	docker run -it mcr.microsoft.com/windows/nanoserver:1903 cmd.exe
	   echo "Hello World!" > Hello.txt
	   exit
	docker ps -a
	docker commit <containerid> helloworld
	docker images
	docker run --rm helloworld cmd.exe /s /c type Hello.txt
	
	docker pull hello-world
	docker run hello-world
	
	curl.exe -LO https://github.com/dockersamples/node-bulletin-board/archive/master.zip
	tar.exe xf master.zip
	cd node-bulletin-board-master\bulletin-board-app
```


```
docker --version
docker info
systemctl status docker
service docker status

docker ps
docker images
docker volume list
```

Rodar daemon separadamente
```
service docker stop

nohup docker daemon -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock &

systemctl start docker

docker info
usermod -aG docker
usermod -aG docker $USER
```

Correção windows 10
```
$Path = "C:\ProgramData\Docker\cli-plugins"
$dir = get-item -Path 'C:\ProgramData\Docker\cli-plugins'
$acl = $dir.GetAccessControl('Access')
$username = 'mingal\lorduakiti'
$AccessRights = New-Object System.Security.AccessControl.FileSystemAccessRule($Username,'Modify','ContainerInherit,ObjectInherit','None','Allow')
$Acl.SetAccessRule($AccessRights)
Set-Acl -path $Path -AclObject $Acl
```
    .. ou
```
$path = "C:\ProgramData\Docker\cli-plugins"
$permission = "AzureAD\myuser@mydomain.com","FullControl","Allow"
(Get-Acl $path).SetAccessRule((New-Object System.Security.AccessControl.FileSystemAccessRule $permission)) | Set-Acl $path
```

Acessar shell de comandos de container
```
docker container attach nome_container
.. ou
 docker container exec -it nome_container /bin/sh
```
