**VIOLAÇÃO DE SENHAS**
(dictionary attack, rainbow table, brute force)

Password cracking (ataques offline) ou password guessing (ataques on-line), a violação de senhas (password attack)

[THC HYDRA](https://www.thc.org/thc-hydra/)

[JOHN THE RIPPER (JTR)](https://www.openwall.com/john/)

[PASS THE HASH TOOLKIT](https://www.openwall.com/john/)



**CRACKING LINUX USER PASSWORD**

The linux user password is saved in /etc/shadow folder. So to crack it, we type :

`john /etc/shadow`

  

**Cracking Password Protected ZIP/RAR Files**

First, go to the directory of the file. Use this command :

`zip2john zipfile > output.txt`

(If it is a RAR file, replace the zip in the front to rar.)

Replace the "zipfile" with the name of the zip file you are trying to crack and replace the "output.txt" with any name that is a .txt format.

After that command, you will see that it would have maked a text file. The hashes are stored in that file. To crack the hash, type :

`john --format=zip hashfilepath`

Again, replace the "hashfilepath" to yours. Mine is just an example. Now, wait, and you can see it is cracked.

Now go in the zip file and put the password.

  

**Decrypting MD5 Hash**

I have my Hash over here :

```
f9be881eddfcf161d35377910b199cf

.. ou

f63f4fbc9f8c85d409f2f59f2b9e12d5 (777777)
```

Now, lets use john to decrypt it. To decrypt it, use this :

`john --format=raw-md5 hashfilepath`

  

**Using Wordlists to Crack Passwords**

I have a wordlist here, and I named it password.txt. To use the wordlist and crack the file, do :

`john --format=raw-shal --wordlist password.txt THEHASHFILE.txt`

  

For more depth analysis visit:-SOURCE

[https://null-byte.wonderhowto.com/forum/cracking-passwords-using-john-ripper-0181420/](https://null-byte.wonderhowto.com/forum/cracking-passwords-using-john-ripper-0181420/)

  

An Ethical hacker should know the penalties of unauthorized hacking into a system. Read more at:- Legality and Ethics

[https://www.note4tech.com/legality-and-ethics](https://www.note4tech.com/legality-and-ethics)