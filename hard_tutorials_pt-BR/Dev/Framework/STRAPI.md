OBS: o usuário de acesso ao banco tem que ser SUPERUSER.
OBS: correção bug YUP
cd node_modules/strapi-utils/
npm install yup@0.28.1 --save
yarn install
nano package.json
cd ..\..\
    ... ou
cd ../../
yarn develop

yarn start
    ... inicializa comando rodando em segundo plano e gravando arquivo de log:
nohup yarn start  > ./strapi.log &


-- configurações
./config/application.json
./config/custom.json
./config/functions/bootstrap.js

./config/environments/**/server.json
./config/functions/cron.js

./config/functions/bookshelf.js
./config/environments/**/database.json
{
  "defaultConnection": "default",
  "connections": {
    "default": {
      "connector": "bookshelf",
      "settings": {
        "client": "postgres",
        "host": "localhost",
        "port": 5432,
        "username": "${process.env.USERNAME}",
        "password": "${process.env.PASSWORD}",
        "database": "strapi",
        "schema": "public"
      },
      "options": {
        "debug": true
      }
    }
  }
}


./config/environments/**/request.json
./config/environments/**/response.json
./config/environments/**/security.json
./config/environments/**/server.json
{
  "host": "localhost",
  "port": 1337,
  "proxy": {
    "enabled": true,
    "ssl": true,
    "host": "example.com",
    "port": 8443
  },
  "cron": {
    "enabled": true
  }
}


-- instalação CLI
.. instalação antiga/global
npm install strapi@alpha -g
strapi -v
docker run --name testemongo -p 27017:27017 -d mongo
strapi new MeuProjeto
cd MeuProjeto
strapi startyarn
.. método de instalação em 2020
create strapi-app my-project --quickstart
.. ou
yarn create strapi-app backend --quickstart --no-run.
.. ou para configurar o DB
yarn create strapi-app my-project
yarn develop

yarn start

http://localhost:1337/admin

-- instalação Docker
criar arquivo "docker-compose.yaml":
version: '3'
services:
  strapi:
    image: strapi/strapi
    environment:
      DATABASE_CLIENT: postgres
      DATABASE_NAME: strapi
      DATABASE_HOST: postgres
      DATABASE_PORT: 5432
      DATABASE_USERNAME: strapi
      DATABASE_PASSWORD: strapi
    links:
      - postgres:postgres
    volumes:
      - ./app:/srv/app
    ports:
      - '1337:1337'

  postgres:
    image: postgres
    environment:
      POSTGRES_USER: strapi
      POSTGRES_PASSWORD: strapi
    volumes:
      - ./data:/data/postgres
    ports:
      - '5432:5432'

docker-compose pull

docker-compose up -d


---- pluguins
yarn strapi install graphql
http://localhost:1337/graphql

yarn create react-app frontend
cd frontend
yarn dev

yarn add react-router-dom

yarn add apollo-boost @apollo/react-hooks graphql react-apollo