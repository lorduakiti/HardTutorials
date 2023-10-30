Verifica versão
```
pg_config --version
```
   .. ou
```
psql
    select version();
    select name, setting from pg_settings where name like '%version%';

    show server_version;
```
    .. ou
```
postgres=# show server_version;
```

Entra no PSQL
```
su - postgres
psql
```

Instalação
Instalando pelo docker
```
docker pull postgres
mkdir -p $HOME/docker/volumes/postgres
docker run --rm  --name pg-docker -e POSTGRES_PASSWORd=senha_db -d -p 5432:5432 -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data postgres

ps aux | grep postgres
```
    .. ou
```
docker run --name database -e POSTGRES_PASSWORD=docker -p 5432:5432 -d postgres:12
```
    .. ou
```
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres

docker run -it --rm --network some-network postgres psql -h some-postgres -U postgres
    SELECT 1;
```

```
Use postgres/example user/password credentials
```
    .. ou
```
docker pull postgres
docker pull dpage/pgadmin4

docker run --name teste-postgres --network=postgres-network -e "POSTGRES_PASSWORD=senha_db" -p 5432:5432 -v /home/nome_usuario/pasta/postgresql:/var/lib/postgresql/data -d postgres
```

Instalando local
```
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'


apt-get update
apt-get install postgresql-contrib-12  postgresql-contrib postgresql
```
    .. ou
```
apt-get install postgresql postgresql-server
```

Inicializando serviço
```
/etc/init.d/postgresql start
```
    .. ou
```
pg_ctlcluster 12 main start
```

Acessando CLI
```
su - postgres
psql
    \conninfo
    \q
```

Desisntalando
```
apt-get remove postgresql --auto-remove
```

Reinicializar
```
/etc/init.d/postgresql restart
/etc/init.d/postgresql reload
```


Para iniciar (START)
Se o Windows for 64, bits será alguma dessas:
- "C:\Program Files (x86)\PostgreSQL\9.0\bin\" ou
- "C:\Program Files (x86)\PostgreSQL\9.4\bin\" ou
- "C:\Program Files (x86)\PostgreSQL\9.6\bin\"

```
cd C:\Program Files (x86)\PostgreSQL\9.6\bin\
pg_ctl -D "C:\Program Files (x86)\PostgreSQL\9.6\data" start
```

STOP:
```
pg_ctl -D "C:\Program Files (x86)\PostgreSQL\9.6\data" stop
```

STOP com segurança:
```
pg_ctl -D "C:\Program Files (x86)\PostgreSQL\9.6\data" stop -s
```

RESTART:
```
pg_ctl -D "C:\Program Files (x86)\PostgreSQL\9.6\data" restart -s
```

TROCAR PORTA:
C:\Program Files (x86)\PostgreSQL\9.6\data
..abra o arquivo postgresql.conf e altere a propriedade "port"

ENTRAR PELO TERMINAL
```
psql -p 5432 -U postgres
psql -p 5433 -U postgres
```

--- PGTune
postgresql.conf
```
# DB Version: 10
# OS Type: linux
# DB Type: oltp
# Total Memory (RAM): 16 GB
# CPUs num: 1
# Data Storage: hdd


max_connections = 300
shared_buffers = 4GB
effective_cache_size = 12GB
maintenance_work_mem = 1GB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 100
random_page_cost = 4
effective_io_concurrency = 2
work_mem = 6990kB
min_wal_size = 2GB
max_wal_size = 8GB
```

ALTER SYSTEM writes the given parameter setting to the postgresql.auto.conf file, which is read in addition to postgresql.conf
```
# DB Version: 10
# OS Type: linux
# DB Type: oltp
# Total Memory (RAM): 16 GB
# CPUs num: 1
# Data Storage: hdd


ALTER SYSTEM SET
max_connections = '300';
ALTER SYSTEM SET
shared_buffers = '4GB';
ALTER SYSTEM SET
effective_cache_size = '12GB';
ALTER SYSTEM SET
maintenance_work_mem = '1GB';
ALTER SYSTEM SET
checkpoint_completion_target = '0.9';
ALTER SYSTEM SET
wal_buffers = '16MB';
ALTER SYSTEM SET
default_statistics_target = '100';
ALTER SYSTEM SET
random_page_cost = '4';
ALTER SYSTEM SET
effective_io_concurrency = '2';
ALTER SYSTEM SET
work_mem = '6990kB';
ALTER SYSTEM SET
min_wal_size = '2GB';
ALTER SYSTEM SET
max_wal_size = '8GB';
```

Criando usuário
```
createuser -slPE
createuser nome_usuario
createuser --interactivenome_usuario
createuser -h eden -p 5000 -S -D -R -enome_usuario
createuser -P -s -enome_usuario
```
    .. ou
```
psql
    CREATE USERnome_usuario;
    CREATE USER nome_usuario WITH PASSWORD '123456';
    CREATE USER nome_usuario WITH PASSWORD '123456' VALID UNTIL '2005-01-01';
    CREATE USER nome_usuario WITH PASSWORD '123456' CREATEDB;
    CREATE USER nome_usuario SUPERUSER INHERIT CREATEDB CREATEROLE;
```

Alterando senha usuário padrão "postgres"
```
su -u postgres psql
alter user postgres with encrypted password 'senha';
```
    .. ou
```
sudo passwd postgres
su postgres
psql -c "ALTER USER postgres WITH PASSWORD 'nova_senha'" -d template1
```
    .. verifica a senha do admin
```
cat /etc/psa/.psa.shadow && echo
```

Lista usuários
```
\du
```
    .. ou
```
psql
    SELECT * FROM pg_user;
```

Arquivo de configuração de acesso ao DB
```
nano /var/lib/pgsql/data/pg_hba.conf
nano /etc/postgresql/12/main/pg_hba.conf
```
    .. ou
```
psql -t -P format=unaligned -c 'show hba_file';
```
    .. ou
```
psql
    SHOW hba_file;
```

```
sed -i 's/host\s*all\s*all\s*127.0.0.1\/32\s*ident\s*sameuser/#&/' /var/lib/pgsql/data/pg_hba.conf && echo -e "\n# Allow password connections from localhost\nhost all all 127.0.0.1/32 md5" >> /var/lib/pgsql/data/pg_hba.conf && /etc/init.d/postgresql restart
```

Monitoramento
```
dpkg -s postgresql | grep Status
```

Lista bases de dados
```
psql
    \l
```

Driver ODBC

Instalar o SGBD PostgreSQL no Debian 10 Buster
```
sudo apt update
sudo apt -y upgrade


apt install curl ca-certificates gnupg
curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sh -c "echo 'deb http://apt.postgresql.org/pub/repos/apt/ buster-pgdg main' >> /etc/apt/sources.list.d/pgdg.list"

apt-get update
apt -y instalar postgresql-12

systemctl status postgresql

sudo -i -u postgres
psql
      \l
      \q
      \d
```
      
Enable remote access      
```
ss -tunelp | grep 5432
```
    .. ou
```
ss -nlt | grep 5432
```
    
```
nano /etc/postgresql/12/main/postgresql.conf
   # Add below line under CONNECTIONS AND AUTHENTICATION section.
   listen_addresses = '*' # Don't do this if your server is on public network
   # Specify server IP Address
   listen_addresses = '10.10.0.2' # Recommended for LAN connections to DB Server
```

```
systemctl restart postgresql
pg_isready
systemctl status postgresql
```

UFW firewall, allow port 5432 for network connections.
```
ufw allow 5432/tcp
```


Add a test database user:
```
su - postgres
createuser test_user1
```
Add the test database and grant ownership to 
```
test_user1:
createdb test_db -O test_user1
```

Set user password:
```
psql
      alter user test_user1 with password 'DBUserPassword';
```
      
Login to test_db database:
```
psql -l  | grep test_db
psql test_db
```

Create a table and add some dummy data:
```
create table test_table ( id int,first_name text, last_name text );

insert into test_table (id,first_name,last_name) values (1,'John','Doe');
```

Show table data:
```
select * from test_table;
```

Drop our test database
```
dropdb test_db
```

Comandos do sistema
```
systemctl start postgresql
systemctl restart postgresql
systemctl stop postgresql
systemctl reload postgresql
```

Securing and Configuring PostgreSQL Database
```
passwd postgres
```
   ..ou
```
su - postgres
psql -c "ALTER USER postgres WITH PASSWORD 'securepass_here';"
```

Configuring Client Authentication
```
nano /etc/postgresql/12/main/postgresql.conf
   # - Connection Settings
   listen_addresses = '*'     # what IP address(es) to listen on;
```

```
nano /etc/postgresql/12/main/pg_hba.conf
   # "local" is for Unix domain socket connections only
   local   all   all   md5
systemctl restart postgresql
```

```
psql -d  test_db  -U test_user
```

```
sudo -u postgres psql -c "SELECT version();"
```

Controle de Usuários
Alterar senha
```
postgres-# ALTER ROLE postgres PASSWORD  'novasenha';
```
ou
```
postgres-# psql -h localhost -U postgres -W -d postgres
postgres-# ALTER USER postgres ENCRYPTED PASSWORD 'novasenha';
```
