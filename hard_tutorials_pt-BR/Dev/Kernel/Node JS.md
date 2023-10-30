Instalação p/ Linux
```
curl -sL https://deb.nodesource.com/setup_12.x | bash -
apt-get install -y nodejs

node -v
```

```
Ctrl+C
```
 (comando p/ sair da execução do arquivo nodejs)

--- nodejs + npm
```
npm init
npm install
npm update
rm -rf node_modules && npm install

npm run start

npm -v

npm init -y
npm install express

npm install -D nodemon
"dev": "nodemon server.js"
npm run dev
```

--- nodejs + yarn
```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```

```
apt-get update
apt-get install --no-install-recommends yarn
```
    .. ou
```
npm install -g yarn
```
    .. ou p/ windows
```
choco install yarn
```

```
yarn --version

yarn install
```

    .. removendo e reinstalando
```
apt remove cmdtest
apt remove yarn

curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

apt-get update  
apt-get install yarn
```

--- nodejs + nvm
```
apt-get update
apt-get install build-essential libssl-dev
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
```

```
nvm --version
```

Instalação p/ windows (usando choco)
```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"

choco install -y nodejs
```


Finalização de serviços
```
ps aux | grep node
kill -9 <PID>
killall -9 node
```

```
apt-get install lsof
lsof -i :3000
```
