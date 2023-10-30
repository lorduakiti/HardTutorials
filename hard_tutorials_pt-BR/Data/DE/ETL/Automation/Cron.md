Verifica comando cron
```
cat /etc/crontab
```

![[Pasted image 20231028164722.png]]


Sintaxe Cron
* Minute (Minuto) – é o minuto da hora em que o comando vai rodar, variando de 0 a 59.
* Hour (Hora) – é a hora em que o comando será executado, variando de 0 a 23.
* Day of the month (Dia do mês) – é o dia do mês em que o comando vai rodar, variando de 1 a 31.
* Month (Mês) – é o mês em que o comando será executado, variando de 1 a 12.
* Day of the week (Dia da semana) – é o dia da semana que você quer que o comando rode, variando de 0 a 7.

Caracteres apropriados em cada arquivo crontab
* Asterisco (*) – define todos os parâmetros de agendamento.
* Vírgula (,) – mantém duas ou mais horas de execução em um único comando.
* Hífen (-) – determina o intervalo da hora ao definir vários tempos de execução de único comando.
* Barra inclinada (/) – cria intervalos pré-determinados de tempo dentro de um intervalo de tempo específico.
* Last (L) – tem o propósito específico de determinar o último dia da semana do mês respectivo. Por exemplo, 3L significa a última quarta-feira.
* Weekday (W) – determina o dia da semana mais próximo de um tempo dado. Por exemplo, 1W significa se o 1° for um sábado, o comando vai ser executado na segunda-feira (3°).
* Hash (#) – para determinar o dia da semana, seguido por um número que varia de 1 a 5. Por exemplo, 1#2 significa a segunda segunda-feira.
* Ponto de interrogação (?) – serve para deixar um espaço.


Verifica agendamentos do sistema
```
ls /etc/cron.*/ -l
```

Verifica agendamentos cron
```
crontab -l
.. ou
crontab -l | tail -n 1
.. ou
sudo crontab -u root -l
.. ou
sudo crontab -u lorduakiti -l
```

Edita agendamentos cron do usuário corrente
```
crontab -e
.. ou
sudo crontab -u root -e
.. ou
sudo crontab -u lorduakiti -e
```

Exemplos de agendamentos
```
.. cria um arquivo específico e escreve uma mensagem no mesmo às 14h30 todos os dias
35 14 * * * echo "test..1" > /home/lorduakiti/test_cron.txt
.. altera um arquivo específico e adiciona uma mensagem no mesmo às 14h30 todos os dias
35 14 * * * /usr/bin/echo "test..2" >> /home/lorduakiti/test_cron.txt
.. altera um arquivo específico e adiciona uma mensagem no mesmo a cada dia
@daily /usr/bin/echo "test..3" >> /home/lorduakiti/test_cron.txt
.. altera um arquivo específico e adiciona uma mensagem no mesmo a cada hora
@hourly /usr/bin/echo "test..3" >> /home/lorduakiti/test_cron.txt
.. altera um arquivo específico e adiciona uma mensagem no mesmo a cada mês
@monthly /usr/bin/echo "test..3" >> /home/lorduakiti/test_cron.txt
.. altera um arquivo específico e adiciona uma mensagem no mesmo a cada semana
@weekly /usr/bin/echo "test..3" >> /home/lorduakiti/test_cron.txt
.. cria um backup a cada minuto todos os dias
* * * * * /bin/sh backup.sh
.. remove todos os arquivos temporários da pasta às 18h30 todos os dias
30 18 * * * rm /home/sydtesting/tmp/*
.. parar de receber esses emails
0 5 * * * /root/backup.sh >/dev/null 2>&1
.. receber a saída de email em uma conta específica
MAILTO="myname@hostinger.com"
0 3 * * * /root/backup.sh >/dev/null 2>&1
.. Fazer um backup de banco de dados à meia noite de cada dia.
0 0 * * * /bin/sh backup.sh
..Fazer um backup de banco de dados duas vezes por dia (às 6 da manhã e às 18h da tarde).
0 6,18 * * * /bin/sh backup.sh
.. Para fazer monitoramentos a cada 6 horas.
0 */6 * * * /scripts/monitor.sh
.. Para executar uma tarefa cron para o arquivo do script localizado no diretório home a cada 10 minutos.
*/10 * * * * /home/user/script.sh
.. Para executar um backup de banco de dados a cada hora em 20 de julho.
0 * 20 7 * /bin/sh backup.sh
.. Para executar um backup de banco de dados à meia noite de toda terça-feira.
0 0 * * 2 * /bin/sh
.. Para executar um comando em Janeiro, Fevereiro e Maio.
* * * 1,2,5 *  /script/script.sh
.. Para executar um comando a cada 5 minutos às 5:00h da manhã, começando às 5:10h.
10-59/5 5 * * * /home/user/script.sh
.. Para executar um comando trimestralmente no primeiro dia às 8:00h da manhã.
0 8 1 */3 * /home/user/script.sh
.. Para definir uma agenda para tarefas múltiplas em um único Cron Job.
* * * * * /scripts/script.sh; /scripts/scrit2.sh
.. Para executar uma certa tarefa toda vez que você inicia o sistema.
@reboot /scripts/script.sh
.. Para executar um comando no primeiro dia de cada mês.
0 0 1 * * /home/user/script.sh
```

Remove agendamentos
```
crontab  -r
..ou
crontab -i
```

Permissões Cron
```
/etc/cron.allow
.. ou
/etc/cron.deny
.. ou
touch /etc/cron.allow
echo "test2" >> /etc/cron.allow
```

