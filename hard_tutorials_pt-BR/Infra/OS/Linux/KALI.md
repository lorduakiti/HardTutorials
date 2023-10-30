**Verificar Arquitetura**

`uname -a`

_32 bits:_
    i386
    i486
    i586
    i686

_64 bits:_
    x86_64


**Verificar versão**
`cat /etc/issue`
    ..ou
`cat /etc/issue.net`

Atualização
```
apt-get update
apt-get upgrade
```

Atualizar o Kali
`sudo apt-get dist-upgrade`

Atualizar os pacotes
`sudo apt-get update && sudo apt-get upgrade`

Atualizar tudo
`sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade`