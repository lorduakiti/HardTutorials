How to install an RDP server on Linux

The Remote Desktop Protocol (RDP) Server can run on Windows and Linux as well. As a result, you can connect to Linux using the RDP protocol.

On Linux, the RDP protocol is not integrated by default as on Windows. Also, Linux does not have some other protocols that are used transparently (invisibly) for Windows users, for example, for network authentication.

However, there are RDP servers that run successfully on Linux.


How to use freerdp-shadow to start an RDP server

To install freerdp on Debian, Kali Linux, Linux Mint, Ubuntu and derivatives, run the command:

	1
	sudo apt install freerdp2-shadow-x11 winpr-utils
To install freerdp on Arch Linux and derivatives, run the command:

	1
	sudo pacman -S freerdp
The executables in different distributions are called slightly differently. In Debian, Kali Linux, Linux Mint, Ubuntu, a file for starting an RDP server is called: freerdp-shadow-x11.

And in Arch Linux, the file for starting the RDP server is called: freerdp-shadow-cli.

But essentially this is same program and they have the same options.

Speaking a bit about rdesktop, we’ve already encountered Network Level Authentication (NLA), that is, network-level authentication. To run the RDP server on Linux, you must choose one of two options:

- start the server without authentication at all
- enable NLA and make the necessary settings for it
To start the RDP server without authentication at all, use the -auth option:

	freerdp-shadow-x11 -auth
If authentication is enabled, PAM is used with the X11 subsystem. Running as root is not required, however, if running as user, only the same user who launched freerdp-shadow can authenticate. Warning: if authentication is disabled, everyone can connect.

Any network service without authentication is a disaster for system security. Therefore, consider how to run freerdp-shadow with NLA support.

First of all, for the NLA, you need to create a file in which there will be a line of the form:

	1
	USER:::HASH:::
We know the Linux username, to calculate the hash, we need to execute a command of the form:

	1
	winpr-hash -u USER -p PASSWORD
For example, the username is mial, and the password is 2, then the command is as follows:

	1
	winpr-hash -u mial -p 2
Calculated hash: 8f33e2ebe5960b8738d98a80363786b0

Create a SAM text file and write a line to it

	1
	mial:::8f33e2ebe5960b8738d98a80363786b0:::
Now run freerdp-shadow-x11 with two options:

- /sam-file:FILE – indicates the location of the NTLM SAM file for NLA authentication
- /sec:nla - force NLA authentication
So my command is as follows:

	1
	freerdp-shadow-x11 /sam-file:SAM /sec:nla
For verification, I connect from Windows:

We are asked to enter credentials – this means that NLA authentication is enabled on the RDP server.

To share not only the entire screen, but only part of it, use the /rect:x,y,w,h option. Where:

- x,y are coordinates of the upper left corner of the rectangle
- w is rectangle width
- h is the height of the rectangle
For example, to share part of the screen 500×500 pixels with coordinates 200,300:

	1
	freerdp-shadow-x11 /sam-file:SAM /sec:nla /rect:200,300,500,500

freerdp-shadow error ‘client authentication failure: -1’

If you get an error when starting the RDP freerdp-shadow server:

	1
	2
	3
	4
	[11:54:02:458] [1921:1925] [ERROR][com.freerdp.client.shadow] - client authentication failure: -1
	[11:54:02:458] [1921:1925] [ERROR][com.freerdp.core.peer] - peer_recv_callback: CONNECTION_STATE_FINALIZATION - peer_recv_pdu() fail
	[11:54:02:458] [1921:1925] [ERROR][com.freerdp.core.transport] - transport_check_fds: transport->ReceiveCallback() - -1
	[11:54:02:458] [1921:1925] [ERROR][com.freerdp.client.shadow] - Failed to check FreeRDP file descriptor
Then you need to create a SAM file and run freerdp-shadow with the /sam-file:SAM /sec:nla options as shown above.


How to start an RDP server with xrdp

To install xrdp on Debian, Kali Linux, Linux Mint, Ubuntu and derivatives, run the command:

	1
	sudo apt install xrdp
To install xrdp on Arch Linux and derivatives, run the following commands:

	1
	2
	3
	4
	sudo pacman -S alsa-lib mesa
	git clone https://aur.archlinux.org/xrdp.git
	cd xrdp
	makepkg -si
To start the xrdp-sesman and xrdp services on any distribution, run the command:

	1
	sudo systemctl start xrdp-sesman.service
Important xrdp files:

- /etc/xrdp/xrdp.ini – configuration file
- /var/log/xrdp.log – log file
We are connected from Windows to Linux with xrdp. Enter the username and password of the Linux user:

Now we can execute commands on a Linux computer as if we were sitting in front of it:

