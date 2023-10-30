
Informações de rede
```
whoami
Test-NetConnection <host> -port <port>
```

Wifi
```
netsh wlan  show network  mode=bssid

netsh wlan connect ssid="YOURSSID" key="YOURPW"
connect name=Profile1 ssid=SSID1
connect name=Profile2 ssid=SSID2 interface="Wireless Network Connection"
```

Lista wifi com senhas salvas
```
Get-NetConnectionProfile
Get-NetConnectionProfile -InterfaceAlias "Ethernet" | Set-NetConnectionProfile -NetworkCategory Public
```

```
netsh wlan show profile
```

```
netsh wlan delete profile ScriptoHouse
```

```
$list=(netsh.exe wlan show profiles)
$list -match ':'
$list=(netsh.exe wlan show profiles) -match ':'

# We start at position 1 in the Array to skip over the first line

For ($x=1; $x -lt $li.count; $x++)
{
    # Examining the String shows the Profile name starts at position 27
    $li[$x]
}
$list=(netsh.exe wlan show profiles) -match '\s{2,}:\s'
```

```
netsh wlan show profile
netsh wlan show profiles key=clear
netsh wlan show profiles name='YOURWiFi'
```
    ..ou
```
Get-NetConnectionProfile
```

Lista adaptadores de rede wifi
```
netsh wlan show interfaces
```
    ..ou
```
Get-NetAdapter
Get-NetAdapter -Name Wi-Fi
```
    ..ou
```
gcm -Noun netadapter | select name, modulename
```

```
Get-NetIPAddress
```

```
netsh wlan connect name=SSID1
netsh wlan connect ssid=YOURSSID name=PROFILENAME
netsh wlan connect ssid=YOURSSID name=PROFILENAME interface="WIRELESS NETWORK CONNECTION"
```

```
netsh wlan disconnect
netsh wlan disconnect interface=Wi-Fi
```
    ..ou
```
Disable-NetAdapter -Name Wi-Fi -Confirm:$false
```

```
Enable-NetAdapter -Name Wi-Fi
```

```
netsh wlan dump
netsh wlan dump > myconfig.txt
netsh exec myconfig.txt
```


Informações de adaptadores de rede
```
ipconfig /all  
```
    .. ou
```
Get-NetAdapter
```

Visualizar as configurações de rede atuais dos adaptadores
```
Get-NetIPConfiguration
```

Listar os adaptadores e seus respectivos endereços IP:
```
Get-NetIPAddress | Sort InterfaceIndex | FT InterfaceIndex, InterfaceAlias, IPAddress -Autosize
```

Obter informações apenas de interfaces configuradas com IPv4:
```
Get-NetIPAddress | ? AddressFamily -eq IPv4 | FT –AutoSize
```

Listar os adaptadores de rede presentes no computador, com seus nomes, descrição, status, endereços MAC e taxa de transmissão:
```
Get-NetAdapter
```

Obter informações sobre um adaptador em particular (por exemplo, Ethernet):
```
Get-NetAdapter Ethernet
Get-NetAdapter -Name 'Ethernet'
```

Testar conexão a um host (ping):
```
Test-NetConnection www.bosontreinamentos.com.br
```

Testar conexão a um host com informações mais detalhadas:
```
Test-NetConnection www.bosontreinamentos.com.br -InformationLevel Detailed
```

Testar conexão a um host e mostrar somente o IP do host, resultado do teste e RTT da conexão:
```
Test-NetConnection www.bosontreinamentos.com.br | Select -ExpandProperty PingReplyDetails | FT Address, Status, RoundTripTime
```

Descobrir informações sobre os servidores DNS associados a um host (similar a NSLOOKUP):
```
Resolve-DNSName www.planetaunix.com.br
```

Traçar a rota até um determinado host remoto (tracert):
```
Test-NetConnection www.planetaunix.com.br -TraceRoute
```

Visualizar as conexões de rede TCP/IP atuais da máquina (similar ao netstat):
```
Get-NetTCPConnection
```

Visualizar somente as conexões de rede TCP/IP estabelecidas (status ESTABLISHED):
```
Get-NetTCPConnection | ? State -eq Established | FT –Autosize
```

Visualizar somente as conexões de rede TCP/IP cuja porta remota é a de número 80:
```
Get-NetTCPConnection | ? RemotePort -eq 80 | FT –Autosize
```

Desabilitar a interface de rede de nome Ethernet:
```
Disable-NetAdapter Ethernet
```
Habilitar novamente a interface Ethernet:
```
Enable-NetAdapter Ethernet
```
Visualizar somente as conexões de rede cujo endereço IP remoto seja diferente de 0.0.0.0:
```
Get-NetTCPConnection | ? RemoteAddress -notlike 0.0.0.0 | FT –Autosize
```

Command as a job and wait for the job to complete
```
If ( (Get-NetAdapter -Name 'Ethernet').Status -ne 'Connected' )
{
      Get-NetAdapter | Disable-NetAdapter -Confirm:$false -AsJob | Wait-Job
      Enable-NetAdapter -Name 'Wi-Fi'-Confirm:$false
      Enable-NetAdapter -Name 'Ethernet 3' -Confirm:$false
}
```


---
Batch file:
```
Netsh WLAN delete profile "SSID"
Netsh WLAN add profile filename=".\WhateverYouWantToCallIt.XML"
Netsh WLAN connect name="$NAME"
```

WhateverYouWantToCallIt.XML file:
```
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.microsoft.com/networking/WLAN/profile/v1">
    <name>SSIDSHOULDGOHERE</name>
    <SSIDConfig>
        <SSID>
            <hex>XXXXXX</hex>
            <name>SSIDGOESHERE</name>
        </SSID>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <MSM>
        <security>
            <authEncryption>
                <authentication>WPA2PSK</authentication>
                <encryption>AES</encryption>
                <useOneX>false</useOneX>
            </authEncryption>
            <sharedKey>
                <keyType>passPhrase</keyType>
                <protected>false</protected>
                <keyMaterial>PASSWORDGOESHERE</keyMaterial>
            </sharedKey>
        </security>
    </MSM>
    <MacRandomization xmlns="http://www.microsoft.com/networking/WLAN/profile/v3">
        <enableRandomization>false</enableRandomization>
    </MacRandomization>
</WLANProfile>
```



```
$WiFi = (netsh wlan show network  mode=bssid |  Select-Object -Skip  3).Trim()  | Out-String

$RegEx = @'
  (?x)
  SSID\s\d+\s:\s(?<SSID>[a-z0-9\-\*\.&_]+)\r\n
  Network\stype\s+:\s(?<NetworkType>\w+)\r\n
  Authentication\s+:\s(?<Authentication>[a-z0-9\-_]+)\r\n
  Encryption\s+:\s(?<Encryption>\w+)\r\n
  BSSID\s1\s+:\s(?<BSSID>(?:\w+:){5}\w+)\r\n
  Signal\s+:\s(?<Signal>\d{1,2})%\r\n
  Radio\stype\s+:\s(?<Radio>[a-z0-9\.]+)\r\n
  Channel\s+:\s(?<Channel>\w+)\r\n
'@

$Networks = $WiFi -split  "\r\s+\n"

$WiFiNetworks = $Networks | ForEach {
  If ($_ -match $RegEx) {
      [pscustomobject]@{
          SSID =  $Matches.SSID
          NetworkType = $Matches.NetworkType
          AuthenticationType = $Matches.Authentication
          Encryption =  $Matches.Encryption
          BSSID1 =  $Matches.BSSID
          SignalPercentage = [int]$Matches.Signal
          RadioType =  $Matches.Radio
          Channel =  $Matches.Channel
      }
  }
}

$WiFiNetworks | Sort SignalPercentage -Descending
```

Get a list of Wifi networks with PowerShell
```
function Get-WifiNetwork {
end {
  netsh wlan sh net mode=bssid | % -process {
    if ($_ -match '^SSID (\d+) : (.*)$') {
        $current = @{}
        $networks += $current
        $current.Index = $matches[1].trim()
        $current.SSID = $matches[2].trim()
    } else {
        if ($_ -match '^\s+(.*)\s+:\s+(.*)\s*$') {
            $current[$matches[1].trim()] = $matches[2].trim()
        }
    }
  } -begin { $networks = @() } -end { $networks|% { new-object psobject -property $_ } }
}
}
Get-WifiNetwork| select index, ssid, signal, 'radio type' | sort signal -desc | ft -auto
```

Repair Windows Server
Start with Windows SFC
```
sfc /scannow
```

If Windows SFC can't fix it, try DISM
```
dism /Online /Cleanup-Image /CheckHealth
```

Corruption detected? Try ScanHealth next 
```
dism /Online /Cleanup-Image /ScanHealth
...
dism /Online /Cleanup-Image /RestoreHealth
dism /source:<spec> /Cleanup-Image /RestoreHealth
```
