Inicialização / Desligamento
```
> reboot
> reboot -f
> shutdown -r now
```

Segurança / Senhas
```
> sudo passwd root
> su
```

**Verifica versão**
`cat /etc/debian_version`
    .. ou
`hostnamectl`
    .. ou
`lsb_release -a`

  

**Verifica lista de referência de arquivos fontes**
```
cat /etc/apt/sources.list
nano /etc/apt/sources.list
```

**Criar um usuário**
_1. No terminal digite o comando abaixo e não esqueça de trocar nomedousuario pelo nome que você deseja criar._
```
adduser nome_usuario
useradd nome_usuario
```

_2. Agora vamos utilizar o comando usermod para adicionar o usuário no grupo sudo._
`usermod -aG sudo nome_usuario`

_3. Agora vamos testar o novo usuário criado e seus poderes como sudo._
`sudo nome_usuario`

Usuário no debian 10
```
deluser <username>
deluser --remove-home <username>
deluser --remove-all-files <username>
```

```
sudo visudo
<username> ALL=(ALL:ALL) ALL
```

ERRO adduser : command not found on Debian
```
ls -l /usr/sbin/ | grep adduser
nano ~/.bashrc
export PATH="$PATH:/usr/sbin/"
source ~/.bashrc
```

**Remover um usuário**
_1. Alternar para o usuário de root:_
`su -`

_2. Use o comando userdel para remover o usuário antigo:_
`userdel -r nome_usuario`

_3. Você pode também excluir esse usuário e seu diretório inicial (/home/nomedousuario):_
`userdel -r nome_usuario`

**Comandos**
Desligamento ou reinicialização
```
poweroff
reboot
shutdown -r now
```

Lista todos os usuários
`getent passwd`
    .. ou
`getent passwd | cut -d \: -f1`
    .. ou
`cat /etc/passwd | grep <user>`

Controle de arquivos
    .. copia arquivos
`cp nome_arquivo.txt nome_arquivo_copia.txt`

    .. copia pastas
`cp -r /home/nome_pasta /var/novo_lugar_da_pasta`

    .. deleta arquivos
`rm nome_arquivo.txt`

    .. deleta pastas
`rm -rf /home/nome_pasta`

Monitoramento
    .. cpu e memória
```
cat /proc/cpuinfo
cat /proc/meminfo
```

    .. lista processos em execução
`top`

   “h” ou “?“: Abre a tela de ajuda do comando top

   “Shift+C“: Navegar pela lista de processos gerados pelo top

   “Shift+L“: Pesquisar pelo nome, basta digitar e apertar “Enter”

   “Ctrl+>” ou “Ctrl+<“: Ordena as colunas do maior para o menor

   “q“: Sair

`systemctl`

`htop`

`netstat`

   -r (route): Mostra a tabela de roteamento utilizado atualmente pelo Kernel

   –g (groups): Mostra informações de membros de grupos multicast por IPv4 e IPv6

   –i (interfaces): Mostra uma babela de todas as interfaces de rede

   -M (Masquerade): Mostra uma lista de conexões mascaradas

   -s (statistics): Mostra um resumo estatístico por protocolo

`vmstat [opções] [intervalo [contador]]`

   -a: Mostra memória ativa e inativa

   -f: Mostra o número de forks desde a última reinicialização do sistema

   -m: Mostra slabinfos

   -s: Mostra estatísticas de memória e contadores

   -d: Mostra estatísticas relacionadas ao disco.

```
ps
ps aux
ps aux | grep nome_processo
ps -ux | grep nome_processo
```

   .. termina/elimina processo
```
kill [PID] killall [nome do processo]
kill -9 23451
```

    .. visualização dos logs do sistema
`tail -f /var/log/syslog`

    .. utilização de memória RAM e SWAP
`free -m -t`

    .. análise de rede
```
apt-get install iptraf
iptraf
```

Listagem de arquivos do diretório
```
ls
ls -la
ls /var/www -l
ls -l | tail -n8
```

Ajuste de permissões
```
chmod
chown
```

Visualizar/editar arquivos
```
tail -f nome_arquivo.log
tail -n10 nome_arquivo.log
tail –c* /home/nome_pasta
tail –n* nome_arquivo.log | sort -r
cat nome_arquivo.log
vi nome_arquivo.log
vim nome_arquivo.log
```

Grava mídia CD/DVD
`mount -o loop /pasta_arquivo_ISO/imagem.iso /media/cdrom0`

Busca de arquivos
```
find /home/user -name arq*
sudo updatedb
locate meu_arquivo.txt
```

Ajuda ou manuais
```
man ls
ls --help
```

Verifica data atual do servidor
`date`

### Como atualizar o Debian

Passo 1. Abra um terminal como administrador ou pressione CTRL + ALT + F1 e faça login como root;

Passo 2. Use os comandos abaixo para atualizar seu sistema atual;
```
apt-get update
apt-get upgrade
apt-get dist-upgrade
```

  

Passo 3. Se quiser conferir como está o arquivo de configuração de programas do gerenciador de pacotes, use esse comando;
`cat /etc/apt/sources.list`

Passo 4. No Debian Jessie, ele deve estar basicamente nesse formato;
```
deb http://security.debian.org jessie/updates main
deb http://ftp.br.debian.org/debian/ jessie main
deb-src http://ftp.br.debian.org/debian/ jessie main
deb http://ftp.us.debian.org/debian jessie main
deb-src http://ftp.us.debian.org/debian/ jessie main
```

Passo 5. Substitua a palavra jessie por stretch, no arquivo **/etc/apt/sources.list**, usando esse comando;
`sed -i 's/jessie/stretch/g' /etc/apt/sources.list`

Passo 8. Use os comandos abaixo para atualizar as informações do gerenciador de pacotes e os pacotes básicos;
```
apt-get update
apt-get upgrade
```

Passo 9. Finalmente, instale qualquer outra atualização disponível, com o comando abaixo. Essa etapa demora bastante e dependendo do que você tem instalado, será necessário responder a algumas perguntas durante o processo;
`apt-get dist-upgrade`

Passo 10. Quando finalizar a instalação, use o comando a seguir para reiniciar o sistema;
`reboot`

Passo 11. Quando o sistema iniciar novamente, use o comando a seguir para verificar as informações atuais do Debian;
`hostnamectl`

Passo 12. O resultado será algo parecido com as informações abaixo;

**Atualização Debian 10**

**Mantendo a versão atual atualizada**
```
apt update
apt upgrade
apt full-upgrade

apt autoremove
```

**Mudando os repositórios**
```
sed -i 's/stretch/buster/g' /etc/apt/sources.list
sed -i 's/stretch/buster/g' /etc/apt/sources.list.d/*.list

nano /etc/apt/sources.list

cat /etc/apt/sources.list
```

**Atualizando sua distribuição**
```
apt update
apt upgrade
apt full-upgrade
```

**Adicionando repositórios contrib e non-free**

Passo 1. Abra um terminal;

Passo 2. Use o comando abaixo para adicionar os repositórios contrib e non-free;
`sed -i 's/main/main contrib non-free/' /etc/apt/sources.list`

Passo 3. Depois, atualize o gerenciador de pacotes com o comando;
`apt update`

Passo 4. Use o comando abaixo para atualizar o sistema;
`apt upgrade`

Passo 5. Finalmente, reinicie o sistema para efetivar qualquer mudança feita pela atualização;
`reboot`

**Comandos debian 10**
```
systemctl poweroff
systemctl reboot
```

Lista portas abertas
`apt-get install nmap`
   .. ou
```
bzip2 -cd nmap-*.tar.bz2 | tar xvf -
cd nmap-*
./configure
make
su root

nmap -v <ip>
nmap -v localhost
nmap -v -p1-65535 localhost
```
    .. ou
```
netstat -ntlp
netstat -aon | findstr "8080"
netstat -aon | grep "8080"
```

Dados de rede
`ifconfig`

    .. eth0 p/ placa de rede ethernet (cabo)

    .. wlan0 p/ placa de wifi

```
lsof -i
netstat -lptu
```

    .. ou
```
iwconfig
cat /etc/network/interfaces
```

  

Abre portas
```
iptables -L
iptables -A INPUT -p tcp --dport 3306 --jump ACCEPT
iptables-save
```

    .. ou
`iptables -I INPUT 1 -p tcp --dport 3306 -j ACCEPT`

    .. ou
```
apt-get install ufw

ufw allow 8080/tcp
ufw enable
ufw status
```

    .. ou
```
firewall-cmd --prmanent --add-port=8080/tcp
firewall-cmd --reload
firewall-cmd --list-ports
```

**Firewall**
`sudo iptables –L`

Liberar o acesso remoto de forma segura usando uma conexão SSH:
`sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`

Para aceitar conexões que serão cruciais para um determinado processo:
`sudo iptables -A INPUT -p tcp --dport <porta> -j ACCEPT`

Permitindo tráfego de entrada em portas específicas
`iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`

...

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

iptables -A INPUT -p tcp --dport ssh -j ACCEPT

iptables -A INPUT -p tcp --dport 80 -j ACCEPT

iptables -L
```

Bloqueio de tráfego 
```
iptables -A INPUT -j DROP
iptables -L
```

Editando iptables
```
iptables -I INPUT 1 -i lo -j ACCEPT
iptables -L
iptables -L -v

iptables -I INPUT 1 -i eth0 -p tcp --dport 8080 -j ACCEPT

service iptables save

service iptables restart
```

Desabilitando Firewall
```
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -F
```

.. ou
```
nano -w /root/fw.stop
echo "Stopping firewall and allowing everyone..."
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
chmod +x /root/fw.stop
/root/fw.stop
```

Verificar status dos discos rígidos (HD/SSD)
```
apt install nvme-cli
nvme smart-log /dev/nvme0
```
percentage_used : 0%
    .. ou 
`apt-get install gnome-disk-utility`

SMART Data & Self-Tests
Reallocated Sector Count (over 60% is bad)

**Ligando WIFI**
```
ifconfig wlan0 up
iwlist wlan0 scan
iwconfig wlan0 essid NOME_WIFI key SENHA
dhclient wlan0
```

---

**1. Exibir todos os usuários existentes na rede**
`/etc/passwd`
    ..ou
`sudo cat /etc/passwd`

**2. Registrar e deletar usuários**
`sudo useradd -m [usuario]`

**2.1 Registrar senhas**
`sudo passwd [usuario]`

**2.2 Remover usuários**
`sudo userdel -r [usuario]`

**3. Criar grupos e adicionar usuários a eles**
```
addgroup [grupo]
sudo usermod -a -G [grupo] [usuario]
```

**4. Associar permissões de grupos a diretórios**
`setfacl opção X:[nome]:Y /[diretório]`

**4.1 Criando permissões aos grupos**
`sudo setfacl -m g:[grupo]:rwx -R /[arquivos]`
    ..ou
`sudo setfacl -m g:[grupo]:rx -R /[arquivos]`

**5. Atualizar informações de usuário**
`usermod`

**5.1 Configurar data de expiração**
`usermod -e [2020-05-26] [usuario]_E`

**5.2 Alterar o local padrão do diretório /home**
```
grep -E '/home/[usuario]' /etc/passwd
usermod -d /var/development/[usuario]
```

**5.3 Inserir informações em uma conta**
```
usermod -c "Analista de Testes" [usuario]
grep -E '[usuario]' /etc/passwd
```

**5.4 Mudar os dados de login**
`usermod -l [usuario_novo] [usuario]`

**5.5 Trancar e destrancar uma conta**
`usermod -L [usuario]`
    ou
`usermod -U [usuario]`

**6. Consultar user logs (registros do usuário)**
```
/var/log
sudo tail /var/log/auth.log
```

**7. Localizar arquivos de usuários**
`find /home -uid [UID] | tee [UID]-files.txt`
    ou
`find /home -uid [GID] | tee [GID]-files.txt`

**7.1 Como obter e gerenciar informações como UID e GID**
```
id user

// uid=1000(user), gid=1000(user), groups=1000(user), 4(adm), 24(cdrom), 27(sudo), 30(dip), 46(plugdev), 118(lpadmin), 127(sambashare)

groupadd -g [GID] [grupo]
groupmod -g [GID-novo] [grupo]
```

**8. Transferir permissões de arquivos**
`chown -R newuser:flavio /home/marcia`

**Verificar espaçoem disco**
`df -H`
.. ou
`du -sh /caminho/*`

**Verificando logs**

Mensagens de Boot
```
journalctl -b
journalctl -b -2
journalctl --list-boots
Intervalo de Temp
journalctl --since "1 hour ago"
journalctl --since "2 days ago"
journalctl --since "2016-01-01 12:00:00 –until “2016-06-30 12:00:00"
journalctl --since yesterday
```

Mensagens de qualquer unit do SystemD, que pode ser serviços, sockets e etc
```
journalctl -u nginx.service
journalctl -u nginx.service -u mysql.service
```

Visualizar Logs em tempo real
```
journalctl -u nginx.service -f
journalctl -n 30 -u nginx.service -f
```

Formato de saída

- _json: irá exibir cada entrada de Log no forma JSON longo._
    
- _json-pretty: irá exibir cada entrada de Log no formato JSON curto e fácil de ler._
    
- _verbose: irá exibir as entradas num formato bem detalhado com todos os campos listados._
    
- _cat: irá exibir a entradas dos Logs em um formato muito curto, sem quaisquer nome de servidores, data hora ou origem._
    
- _short: é o formato padrão. Este formato exibe as entradas de log no estilo Syslog._
    
- _short-monotonic: é similar ao short, mas o campo de tempo data hora é exibido com precisão. Muito similar a data hora do proxy squid._
    

Por usuário
``id uakiti
journalctl _UID=1001``

Por prioridade
`journalctl -b -1 -p "crit"`

Mensagens de erro
`journalctl -p err`
.. ou
`tail -f /var/log/journal/*/*`

Quantidade de espaço ocupado pelo log
```
journalctl --disk-usage
du -hs /var/log/journal/
ls -lath /var/log/journal/*/ | tail -n 2
```

Limpe systemd journals
.. de mais de X dias
`journalctl --vacuum-time=10d`

.. se eles excederem X de armazenamento
`journalctl --vacuum-size=2G`

Fazer integração Rsyslog com Journal
```
nano /etc/rsyslog.conf
$OmitLocalLogging off
nano /etc/rsyslog.d/listen.conf
```

**Configurando fuso horário**
```
timedatectl
timedatectl list-timezones
timedatectl set-timezone America/Sao_Paulo
```