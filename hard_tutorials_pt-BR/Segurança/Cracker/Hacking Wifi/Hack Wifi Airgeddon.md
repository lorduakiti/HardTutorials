**Hack Wireless Network using Airgeddon**

Airgeddon is a multi-use bash script for Linux systems to audit wireless networks.

Features:
- Interface mode switcher (Monitor-Managed).
- DoS over wireless networks with different methods.
- Assisted Handshake file capture.
- Cleaning and optimizing Handshake captured files.
- Offline password decrypt on WPA/WPA2 captured files (dictionary and bruteforce).
- Compatibility with many Linux distros (see requirements section).
- Easy targeting and selection in every section.
- Controlled Exit. 
- Cleaning tasks and temp files. 
- Option to keep monitor mode if desired.
- Multilanguage support and autodetect OS language feature (see supported languages section).
- Help hints in every zone/menu for easy use.
- Auto-update. 
- Script checks for newer version if possible.

First of all clone  **airgeddon** from **git** by executing the following command on the terminal:
`git clone https://github.com/v1s1t0r1sh3r3/airgeddon.git`

Now to launch airgeddon enter the cloned folder and execute command:
`./airgeddon.sh`

After execution it will take some time to check if all the tools are installed  and then press **Enter** when asked.

Now it will ask you to select an interface.

Select  wlan0  for wifi attacks as in my case I have selected 2.

After selecting wlan0 it will give many other options to choose.

You have to select **option 2** to enter into the monitor mode which puts the wlan0 interface in monitor mode to listen to all the available wifi connections  and then press **ENTER** key to continue further.

Now  again it will give some new options and you have to select **option 5** to enter Handshake tools menu.

Now it will present you with more options, now select **option 5** to start capturing handshake and **press ENTER** when asked.

Then it will again ask you to press **ENTER** for selecting the target so **press ENTER**.

Now it will open a new window showing you the list of available targets.

Wait till your target wifi appears and then  hit **ctrl^c**.

Now it will show you the available target list, so select your target by entering the id  NO. given in the **N column**.  

I have selected the network **Tenda_3**  by entering  **6**.After that press ENTER to continue.

Now it will ask to choose the attack type so select  2 for **Deauth  aireplay attack** which will first use deauth attack to disconnect all the client and then capture the handshake between the router and the client.

Again  press **ENTER** to continue which will open two windows.

As you can see below  two windows will open, one is deauth attack window and otrher is capturing handshake.

You have to wait till the WPA Handshake appears on the top right side of the window and then hit **ctrl^c**.

Then it will ask if you get the handshake, so **press y** for YES and then it will ask the path of the capture file which is default set to the last capture so just press **ENTER**.

Now you have to return to the main menu by **pressing  7**

Now you have to crack the password by selecting the **6 option**.

Now it will ask to select a attack for breaking the password, we will select **dictionary attack** to use a dictionary  and then it will  ask if you want to use already selected  BSSID so **press  y** and then it will ask to use the already selected BSSID so **press y** and it will again ask the path of dictionary file.

Here you can give your custom made dictionary or default dictionary present in the kali.

I have used password.lst in the  **/usr/share/nmap/nselib/data/passwords.lst** and then press **ENTER** key to continue.

Now it will start cracking with **aircrack-ng** and if the password is present in the dictionary it will show you the password  as you can see that KEY FOUND**[87654321]**.