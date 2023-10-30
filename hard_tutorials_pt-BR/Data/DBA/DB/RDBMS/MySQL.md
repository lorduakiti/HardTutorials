Acessar terminal de consultas
`mysql -h localhost -u root -p`
   .. ou
`mysql -u <nome_usuario> -p`
   .. ou
`mysql -u <nome_usuario> -p nome_do_banco_de_dados`

Incluindo permissões
`GRANT ALL PRIVELEGES ON *.* TO root@'%' IDENTIFIED BY 'suasenhaaqui';
FLUSH PRIVILEGES;
SHOW databases;
DESCRIBE nome_tabela;`

Cria uma cópia do banco
`mysqldump -u root -p database_name > database_name.sql;`
   .. ou p/ copia somente da estrutura de dados
`mysqldump -u root -p database_name --no-data > database_name.sql;`
   .. ou p/ copia de todos os bancos de dados
`mysqldump -u user -p --all-databases > full_path_to\file.sql`

Importando banco de dados
`mysql -u root -p database_name < database_name.sql;`

Sai do terminal
`EXIT`
   .. ou
`QUIT`

Instalação
`apt install mysql-server`

Configurações de segurança
`mysql_secure_installation`

Monitoramento
`dpkg -s mysql-server | grep Status`