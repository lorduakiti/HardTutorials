[https://miloserdov.org/?p=2255](https://miloserdov.org/?p=2255)

## Npcap is WinPcap for Windows 10

[Npcap](https://github.com/nmap/npcap) is a project from Nmap programmers, a library designed for sniffing (and sending) packages. Is is Windows-based programm. It is based on the WinPcap/Libpcap libraries, but it has improved speed, portability, and security. At its core, Npcap is a new version of WinPcap, with new features and support for modern versions of Windows 10.

Classic WinPcap can capture raw 802.11 frames, but only supports one hardware solution - AirPcap. Npcap can also capture raw 802.11 frames, but supports various wireless adapters.

In addition, Npcap includes related utilities that can set wireless cards into monitor mode in Windows (if the wireless adapter driver supports it).

## Monitor Mode in Windows

As in Linux, in Windows you can set some wireless cards into Monitor Mode. For example, it works for many Alfa. But there are several problems at once:

- programs from the Aircrack-ng package do not know how to work with these interfaces - that is, Airodump-ng is not able to capture any data
    
- there are almost no programs that can do wireless injections, that is, there is no way to perform the most popular attacks.
    

Nevertheless, there is a more or less working combination: Wireshark is able, through Npcap, to set wireless interfaces into monitor mode and capture raw frames of Wi-Fi networks (at least this is written in the official Npcap documentation). Again, it is impossible to do wireless injections, but in clientless attacks (details here and here) we don’t need to do wireless injections, to capture PMKID we need monitor mode, as well as a second wireless interface from which a typical connection attempt will be made (with any password).

## How to set up Npcap to capture raw Wi-Fi frames

So, we need Npcap itself. You can download it [here](https://miloserdov.org/?goto=28847) (this is the official site).

Please note that Npcap and WinPcap should not be installed simultaneously. Therefore, if you already have WinPcap, first remove it. Npcap completely replaces WinPcap and has additional features. If you uninstall WinPcap during the Npcap installation, when prompted whether to restart the computer now, select a manual restart to complete the Npcap installation.

Although it is not written anywhere, but for Npcap, apparently, you need [Microsoft Visual C ++ Redistributable 2013](https://miloserdov.org/?goto=28848).

When installing Npcap, select options:

- Support raw 802.11 traffic
    
- Install Npcap in WinPcap API-compatible Mode

![[Pasted image 20231028144031.png]]

We also need the Wireshark program, which can be downloaded [here](https://miloserdov.org/?goto=28849). If Npcap is installed correctly, then the option to install WinPcap should be inactive:
![[Pasted image 20231028144044.png]]

To work with wireless cards in monitor mode, Npcap uses the ‘Native 802.11 WLAN’ interface, which is obsolete in Windows 10. And although there is evidence that in Windows 10 you can still use Native 802.11 WLAN, you also can try to install all this in Windows 8 for better results.

You may also need drivers for your wireless card, for example, for Alfa I downloaded drivers here: [alfa.com.tw/files/?dir=%5B1%5D%20WiFi%20USB%20adapter](https://miloserdov.org/?goto=28850)

Examples of adapters tested by the authors of the program: [https://secwiki.org/w/Npcap/WiFi_adapters](https://miloserdov.org/?goto=28851)

## How to capture PMKID in Windows

Plug your Wi-Fi adapter.

Run Wireshark as administrator:
![[Pasted image 20231028144102.png]]

Now find the Capture menu and select Options there:
![[Pasted image 20231028144118.png]]

If your adapter supports monitor mode and if you have installed everything correctly before, then you should have a checkbox in the Monitor Mode column opposite the wireless interfaces.

I have it for my Wi-Fi interfaces (as you can see in the screenshot), but as soon as I select checkbox, a tick disappears. I tried three Wi-Fi adapters with different chipsets, tried virtual machines, tried on a real computer, even installed Windows 8 specifically and tried there. The result is always the same - the check mark disappears immediately. One can begin to doubt whether it works at all, but [here](https://miloserdov.org/?goto=28852) the authors write very confidently what works.

Therefore, we will proceed from the assumption that this also worked for you.

In order for the access point to send Message 1 of a handshake (which can contain PMKID), you need to connect to it from another wireless interface — you can choose any password, since the password does not affect the Message 1 of a handshake.

After the data is captured, you can filter the desired frame directly in Wireshark, for this use a filter (for details on working with wireless frames, see the article “[How to extract all handshakes from a capture file with several handshakes](https://miloserdov.org/?p=1047)”):

  

|   |
|---|
||

  

After extracting the PMKID, brute-force can be started into Windows, into Aircrack-ng (as shown [here](https://miloserdov.org/?p=2122)) or into Hashcat (as shown [here](https://miloserdov.org/?p=2100)).

## How to set a Wi-Fi adapter into monitor mode in Windows

If you also have problems with capturing raw Wi-Fi frames, then as a consolation prize, you can switch your Alfa to monitor mode in Windows - there are no problems with that. The problem is that any use of this monitor mode is completely missing: Airodump-ng does not understand these interfaces. And even Wireshark, which seems to be working with Npcap, does not understand this monitor mode - it is necessary that Wireshark sets the card into monitor mode.

Therefore, for those who, like me, did not succeed, this monitor mode comes as a consoling (and useless) prize.

To enable it, open a command prompt from the administrator and go to the folder C:\Windows\System32\Npcap\:

  

|   |   |
|---|---|
|1|cd C:\Windows\System32\Npcap\|

  

See the names of the wireless interfaces:

  

|   |   |
|---|---|
|1|netsh wlan show interfaces|

![[Pasted image 20231028144157.png]]

I renamed my interface to awus052nh, by default it may be called “Wireless Network” or something similar. As it is written in the help, the WlanHelper.exe program must understand both the Interface Name and the GUID, which is shown a couple of lines lower. But my WlanHelper.exe categorically does not accept the name of the interface, an error appears

  

|   |   |
|---|---|
|1<br><br>2|Error: makeOIDRequest::strAdapterGUID error, the adapter name is incorrect.<br><br>Failure|

  

Although it accepts GUID normally.

Therefore, in the subsequent commands, instead of the name, I will use the GUID (replace it with your own).

To view the current monitor mode, enter:

  

|   |   |
|---|---|
|1|WlanHelper.exe 1eff05d6-56c5-4a12-bb4b-d96a7ef0af2f mode|

  

To put in monitor mode:

  

|   |   |
|---|---|
|1|WlanHelper.exe 1eff05d6-56c5-4a12-bb4b-d96a7ef0af2f mode monitor|

  

Checking:

  

|   |   |
|---|---|
|1|WlanHelper.exe 1eff05d6-56c5-4a12-bb4b-d96a7ef0af2f mode|

  

Everything worked out:
![[Pasted image 20231028144213.png]]

As simple as it’s useless …

## Conclusion

As I said, I did not manage to capture PMKID. If someone succeeds according to this instruction - please write in the comments. Maybe someone saw what was done wrong by me and why Wireshark cannot use the monitor mode? I would be grateful for any advice.

Anonymous says:

[February 7, 2019 at 11:26 pm](https://miloserdov.org/?p=2255#comment-3023)

[https://github.com/nmap/nmap/issues/757](https://github.com/nmap/nmap/issues/757)

delete 2 dlls: wpcap.dll, Packet.dll in C:\WINDOWS\System32 and in C:\WINDOWS\SysWOW64

(possibly, whireshark will not work; then uninstall wireshark with winpcap, install npcap, install vc++2013, install wireshark)

[Reply](https://miloserdov.org/?p=2255&replytocom=3023#respond)

- Alex says:
    
    [February 8, 2019 at 3:34 am](https://miloserdov.org/?p=2255#comment-3025)
    
    Wow, man! Thanks a lot!    Finally, I resolved the issue!
    
    My problem was extra wpcap.dll and Packet.dll files in C:\WINDOWS\System32 and in C:\WINDOWS\SysWOW64 in addition to the same files in C:\Windows\SysWOW64\Npcap\ and C:\Windows\System32\Npcap\
    
    Now I can set my Wi-Fi adapters in monitor mode in Windows and perform tests.
    
    [Reply](https://miloserdov.org/?p=2255&replytocom=3025#respond)