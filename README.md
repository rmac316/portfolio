# HTB: Meow Walkthrough

## Mission Objective
Gain access to machine to obtain flag

## Reconnaissance

### Host Discovery
In this lab, the IP address of the target machine is provided

#### Target IP Address(es)
Target 1: 10.129.1.17


### OSINT


### Metasploit (Exploit-DB)


### Nmap

#### Commands Ran
Scan 1
```
nmap -A -T5 10.129.1.17
```
- *a scan is being run on the IP address 10.129.63.79 with the -A switch, which performs OS detection, version detection of services, script scanning for vulnerabilities and traceroute all in one option.*

- *the -T5 essentially speeds up the process by running the scan in parallel, but is more prone to errors.*

Scan 2
```
nmap -n -sV -Pn --script "*telnet* and safe" -p 23 10.129.1.17
```
*a scan is being run on the IP address 10.129.63.79 with several switches:*

- *-n: tells nmap to skip DNS resolution, relying solely on the IP address provided in the command*

- *-sV: tells nmap to scan for a service version*

- *Pn: tells nmap to disable "port knocking" and instead of sending a specific sequence of packets, nmap will scan the port directly*

- *--script "*telnet* and safe": tells nmap to use its scripting engine to find all scripts whose names contain "telnet" and are categorized as "safe" scripts that will not cause any harm to the machine being targeted.*

- *-p 23: specifies to do this scan on port 23*

*OPTIONAL*
- ```| -tee nmap.results_focused``` : saving the results of the scan to a file named nmap.results_focused*


#### Scan Results
Scan 1 Results
![alt text]()

*Results of the first nmap scan shows Port 23 as the only open port, with 4 other ports being filtered.*

Scan 2 Results
![alt text](image-2.png)

*Results of second scan shows the service as "telnet"

### Dirbusting


### Ports
#### 23/tcp filtered telnet

```
telnet [target IP address] [port number]
```





## Initial Access/Foothold
Upon using the "telnet [target IP] [port number]" command, a telnet connection was made to the target device. A login prompt appeared: 

![alt text](image-4.png)

Testing for weak/default usernames and password, the name "root" was entered into the login prompt. After selecting enter, root access to the target machine was granted. It appears there is no password configured for the root user on the target machine.

## Privilege Escalation
### Normal / Low-Privelege User
not encountered/needed

### Admins / Root 
Granted root access by providing "root" as user name with no password.

## Credentials

user: root  
pass: none

### Hydra

## Flag

Results
![alt text](image-5.png)

## Summary
A target IP address was provided with instructions to retrieve the flag located on the target machine. 

To begin, an nmap scan was ran to identify open ports on the target machine. The results of this scan showed 5 ports, with port 23 being open, and the other 4 showing a "filtered" state.

A subsequent nmap was conducted to do a more in-depth search into the telnet service on port 23. 

Seclists were installed on the attacker machine to provide different username and password lists.

A telnet connection to port 23 was established between the attacker and the target machine, and a login prompt appeared. A username of "root" was entered, and access was granted without a password. 

Once inside the device, the ```ls``` command was used to display folders in the current directory. Two items were present: a directory called "snap" and a file called "flag.txt".

A ```cat``` command was ran on flag.txt, which provided our flag of **b40abdfe23665f766f9c61ecba8a4c19**

## Conclusions
Open ports were easily identifiable on the target machine. Once open ports and services running were identified, attempts to establish connection with the target machine proved successful with minimal resistance. Root access was immediately granted and access to the flag was rapid. 

It is recommended that all user accounts implement password policies that include password complexity and history

## Lessons Learned/Areas of Improvement
**Lessons Learned**

Ran into extensive issues establishing connection to target machine over VPN. Research into an explanation of the several failed connections showed that root privilege was required to execute the openvpn command. After several failed attempts to run sudo followed by the openvpn command, trying the ```sudo su - ``` command and entering the password prior to running the sudo openvpn command proved successful.

**Areas of Improvement**

Too heavily dependent on instructor guidance for OSINT recon and command execution. Continue to work on independent research and recon techniques. 
