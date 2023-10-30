How to hack in Windows (miloserdov.org)

There are not any free Windows-based tools for wireless audit, are there?

There are!

[Router Scan](https://en.kali.tools/?p=244) – free Windows program with GUI to scan networks and hack routers – got new amazing features. Now you can perform Wi-Fi security audit from Windows.

  

## New Router Scan by Stas'M features

In Router Scan since v2.60, wireless audit capabilities have been implemented, they include brute-force attack against WPA/WPA2 key of a network, obtaining a password using the WPS PIN, and Pixie Dust attack.

  

## Download the latest Router Scan

Go to the [program page](https://miloserdov.org/?goto=28359) and click the **Mirror download** button:
![[Pasted image 20231028142615.png]]

Unzip the archive and run the **RouterScan.exe** file.

  

## What do I need to use the wireless audit capabilities of Router Scan?

**Programs**

  

If you see error messages when trying to use the wireless audit functions:

  

  

|   |   |
|---|---|
|1<br><br>2|[-] Raw packet capture not available.<br><br>[*] Please install WinPcap and try again.|

  

Then you need to install WinPcap, a link to download (choose the version for Windows): [https://www.winpcap.org/install/default.htm](https://miloserdov.org/?goto=28360)

  

**Hardware**

  

To brute-force WPA-PSK key (in other words, the password from the Wi-Fi network), any Wi-Fi card should approach, since the Router Scan just try to connect using different passwords in ordinary way. If you have several wireless dongles you should give preference to one that sees more networks and keeps the signal better.

  

In the attacks on the WPS PIN when using [Alfa AWUS036NHA](http://rover.ebay.com/rover/1/711-53200-19255-0/1?icep_ff3=9&pub=5575132165&toolid=10001&campid=5338241738&customid=&icep_uq=Alfa%2BAWUS036NHA&icep_sellerId=&icep_ex_kw=&icep_sortBy=12&icep_catId=&icep_minPrice=&icep_maxPrice=&ipn=psmain&icep_vectorid=229466&kwid=902099&mtid=824&kw=lg)

..imagem

 I received an error message:

  

  

|   |   |
|---|---|
|1<br><br>2|[-] Failed to start raw packet capture.<br><br>[-] Try updating your Wi-Fi driver and/or installing Npcap in WinPcap-compatible mode.|

  

Since my second [Alfa AWUS052NH](http://rover.ebay.com/rover/1/711-53200-19255-0/1?icep_ff3=9&pub=5575132165&toolid=10001&campid=5338241738&customid=&icep_uq=Alfa%2BAWUS052NH&icep_sellerId=&icep_ex_kw=&icep_sortBy=12&icep_catId=&icep_minPrice=&icep_maxPrice=&ipn=psmain&icep_vectorid=229466&kwid=902099&mtid=824&kw=lg)
.. imagem

 adapter works with Router Scan fine, it is more long-range, and I mostly use it, including Linux, I did not try to cope my issues with AWUS036NHA. Perhaps the problem could be solved in the recommended way. By the way, write in the comments, what wireless adapters work with Router Scan well, and with what problems you occurred.

  

Since WinPcap also contains the driver, I would not be surprised if even wireless cards that are unsuitable for use in Wi-Fi auditing in Linux due to driver limitations are suitable for WPS PIN attacks in Windows. I cannot verify this, because at hand there is no ‘usual’ Wi-Fi adapter.

  

Built-in Wi-Fi adatper [Intel Corporation Centrino Advanced-N 6235 (rev 24)](https://miloserdov.org/?goto=28361) (in Linux it supports monitor mode and injection) also works well with Router Scan.

  

**Configuring Router Scan**

  

For some of our attacks, we need information from **[3WiFi](https://miloserdov.org/?goto=28362)**. Guest access is enough for our needs. Go to the Router Scan settings and enter "**antichat**" as **Username** and **Password**:
![[Pasted image 20231028142854.png]]
## Overview of wireless networks in Router Scan

In Router Scan, since version v2.60, the **Wireless Networks** tab has appeared:
![[Pasted image 20231028142914.png]]

In the **Interface** field, select the Wi-Fi adaper that you want to use for auditing Wi-Fi networks. If you check **Enable discovery**, data will be collected about available access points within reach. To prevent access points from disappearing from the list, tick the **Cumulative mode**:
![[Pasted image 20231028142931.png]]

The **Active** column marks (seemingly) the networks to which you are currently connected.

  

Please note that we have access to advanced information about access points. You can see the exact model (**WSC Name** and **Model fields**) for the AP with WPS.
![[Pasted image 20231028143008.png]]

Since only access points with WPS are suitable for hacking, it is necessary to select those with a version number in the **WPS** column, and nothing is written in the **Locked** column (i.e. WPS for this AP should not be blocked), an example of an appropriate point access:
![[Pasted image 20231028143027.png]]

Click on the selected AP with the right mouse button, select **Obtain key with WPS …**:

  

  

  

If you already have a WPS PIN (or several pins), enter them in the **PINs** field. If you do not have a pin, then close this window.

  

Two options are also available in the context menu:

  

- **WPS PIN Companion**
    
- **WPS PIN from 3WiFi**
    

The first calculates the PIN for a given access point by algorithms:
![[Pasted image 20231028143103.png]]

![[Pasted image 20231028143121.png]]

As you can see, the guessed pin is 36158805.

  

When you click on **WPS PIN from 3WiFi**, the following window is displayed to us:
![[Pasted image 20231028143136.png]]

In it again 36158805 and, apparently, the percentages of coincidence.

  

Let's return to the option **Obtain key with WPS …**:
![[Pasted image 20231028143153.png]]

Our pin is entered automatically, press **Start audit**.

  

As you can see, the password is successfully cracked:
![[Pasted image 20231028143210.png]]

This is indicated by the lines:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3|[+] WPS PIN: 36158805<br><br>[+] SSID: Keenetic-8955<br><br>[+] Key: WJ5btEX3|

  

The password from Wi-Fi is WJ5btEX3.

  

## Brute-force password from any Wi-Fi network with Router Scan

The previous method is fast, but works only for networks with unlocked WPS.

  

For all other access points, a more universal but slower method is available. Its essence lies in the fact that Router Scan tries to connect to the Wi-Fi network with a password that it takes from the dictionary. If the connection was successful, then the password is guessed, if the connection did fail, then the program proceeds to the next password and tries it, etc. Next, until the password is cracked or the dictionary is completed. Every attempt takes a few seconds - it's slow.

  

To start the attack, right-click on the access point of interest to you and select **Brute-force network …**:
![[Pasted image 20231028143242.png]]

The attack must begin with the selection of the dictionary file. To do this, opposite the **Dict** field, click **(click to load)** and select the dictionary file. With the program comes a small dictionary (file **wlanpass.txt**) for 90 words. You can use it.

  

Successful password cracking:
![[Pasted image 20231028143256.png]]

By the way, so that attempts do not stop, click on **More**, then **Maximum error counts** and enter **0** for unlimited attempts:
![[Pasted image 20231028143309.png]]

## View passwords from Wi-Fi to which the computer was previously connected

Router Scan allows you to get the passwords from the networks you was previously connected to. To do this, select the **Resolve stored keys** option from the context menu:
![[Pasted image 20231028143333.png]]

## How to determine the location of Wi-Fi

To do this, in the Router Scan select the **Locate AP by BSSID** option, the following window will appear:
![[Pasted image 20231028143349.png]]

For example, click on **Google Maps**:
![[Pasted image 20231028143402.png]]

## Problem solving

Sometimes when I attacked Wi-Fi, there were endless messages:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4|[*] Associating with AP...<br><br>[-] Association failed.<br><br>[*] Associating with AP...<br><br>[-] Association failed.|

  

If you stop and restart the attack, this error usually disappears.

  

The Wireless Networks tab does not work on Linux.

  

## Conclusion

Router Scan Stas'M is a unique and easy to use program, it has no analogues for either Windows or Linux. New Router Scan features in many ways are also unique for Windows. However, Linux OS can offer additional tools for attacks on the Wi-Fi network.

  

  

  

## Router Scan 

  

Router Scan is able to find and identify a variety of devices from large number of known routers and that the most important thing is to get from them useful information, in particular the characteristics of the wireless network: a method of protecting the access point (encryption), access point name (SSID) and access point key (passphrase).

  

Also it receives information about the WAN connection (useful when scanning a local network) and show the model of router.

  

Getting information occurs in two possible ways:

  

The program will try to guess a pair of username/password to the router from a list of standard passwords, thereby get access.

Or the vulnerabilities (bugs) will be used against the router model, allowing to get the necessary information and/or bypass the authorization process.

  

Wireless network detection and audit were added beginning with the [version 2.60](https://miloserdov.org/?p=712) including 802.11a/b/g/n standarts, you will need either an integrated or external Wi-Fi interface to use these functions.

  

[3WiFi](https://miloserdov.org/?p=746) service functions were also added to achieve best wireless audit results, WPA/WPA2 network key brute-force and WPS PIN audit along with Pixie Dust attack.

  

Homepage: [http://stascorp.com/load/1-1-0-56](https://en.kali.tools/?goto=69)

  

Author: Stas'M Corp.

  

License: FREE, AS IS

  

## How to install Router Scan

  

**Installation on Kali Linux.**

  

First install Wine:

  

  

|   |   |
|---|---|
|1<br><br>2|sudo dpkg --add-architecture i386 && sudo apt update && sudo apt install wine32 winbind<br><br>wine --config|

  

  

[Download the archive](https://en.kali.tools/?goto=69), unpack it using password

  

  

|   |   |
|---|---|
|1|Stas'M Corp.|

  

  

Run **RouterScan.exe** using Wine.

  

**Installation on Windows.**

  

[Download the archive](https://en.kali.tools/?goto=69), unpack it using password

  

  

|   |   |
|---|---|
|1|Stas'M Corp.|

  

  

Run:

  

  

|   |   |
|---|---|
|1|RouterScan.exe|

  

## Router Scan Screenshots

![[Pasted image 20231028143511.png]]

![[Pasted image 20231028143549.png]]

![[Pasted image 20231028143601.png]]

![[Pasted image 20231028143614.png]]

![[Pasted image 20231028143626.png]]

![[Pasted image 20231028143639.png]]

https://en.kali.tools/wp-content/uploads/2016/12/x14.jpg.pagespeed.ic.QK_kphsPsv.webp


## Router Scan Tutorials

[How to hack routers in Windows (Router Scan by Stas’M manual)](https://miloserdov.org/?p=766)

[How to hack Wi-Fi in Windows](https://miloserdov.org/?p=712)

[How to install Router Scan](https://miloserdov.org/?p=784)

[How to collect Location, Country or ISP IP Ranges](http://miloserdov.org/?p=17)

[3WiFi: open database of Wi-Fi Access Points passwords](https://miloserdov.org/?p=746)