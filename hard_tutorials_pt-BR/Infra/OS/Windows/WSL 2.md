```
> echo 'export LIBGL_ALWAYS_INDIRECT=1' >> .bashrc   
> echo "export WSL_HOST=$(tail -1 /etc/resolv.conf | cut -d' ' -f2)" >> .bashrc  
> echo 'export DISPLAY=$WSL_HOST:0' >> .bashrc
> sudo apt install vim-gtk3 tilix dbus-x11 git
> source .bashrc
> echo $DISPLAY
> gvim
> tilix .. money dark
```

senha root wsl
```
> wsl -u root
> wsl -d Debian -u root
> passwd
> exit
```

restartando serviço wsl
```
net stop LxssManager
net start LxssManager
wsl --list --running
```

reboot
`Get-Service LxssManager | Restart-Service`

Verificar Instalação
`wsl -l -v`

Desativar Máquinas
```
wsl -t <distribution name>
wsl --shutdown
```

Liberar Memória
`echo 1 | sudo tee /proc/sys/vm/drop_caches`

Acessar o sistema de arquivos do Linux pela rede do Windows
`\\wsl$`

Acessa o sistema de arquivos do Windows 10
`/mnt`