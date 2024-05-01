# CompTIA Security+ Labs
## Assisted Lab 2: Scanning and Identifying Network Nodes
**Scenario: In this activity, you will use common network tools, such as local commands and the Nmap network mapper, to discover other hosts on the local network. Next, you will conduct a banner grabbing exercise to identify specific services on the hosts. Finally, you will use DNS tools to gather name resolution information.**

### Identify local network configuration
Determine the configuration of the local host and its subnet, using tools such as:
* ifconfig
* ip
* arp
* netdiscover
* pathping

Run the scans from the KALI Linux VM and the DC1 Windows Server.

#### 1. On the PT-1 Kali VM, sign in as root using Pa$$w0rd as the password
#### 2. From the top bar, select the Terminal Emulator icon Gnome Terminal Emulator icon button.
#### 3.Run the ifconfig command to display the interface configuration. Note the IP address assigned to the eth0 interface.
```ifconfig```
#### 4. Run the ip a command to display the same information using the newer ip a tool:
```ip a```
#### 5. What is the IP address for the eth0 adapter of the PT1-Kali Linux virtual machine? (answer format: 1.2.3.4)
*we can see the second entry of the ```ip a``` command output has "eth0" which is the IP address we are looking for. next to "inet" we can see an IPv4 IP address, which matches the required format of the answer*

**Answer: 10.1.0.192**
#### 6. Run the following command to identify the default gateway:
```ip route show```

Because the network uses DHCP to provide client addresses, the local machine has been configured with a default gateway address automatically.
#### 7. What is the IP address of the router? (format 1.2.3.4)
*By entering the command ```ip route show``` we get a response that reads "default via 10.1.0.254". Since the router is typically the default gateway of a network, this is likely our answer.*

**Answer: 10.1.0.254**

#### 8. Run the ```arp -a``` command to check the ARP cache to display other hosts local to this subnet.
```arp -a```
#### 9. What isthe IP address for the DC1.corp.515support.com virtual machine? (format 1.2.3.4)
*Running the command outputs an IP address for the DC1.corp.515support.com machine is 10.1.0.1*

**Answer: 10.1.0.1**
#### 10. Run the ```ip neighbor``` command to display similar information using the newer ip tool.
```ip neighbor```

The ARP cache shows only machines that have communicated with the local host. To verify whether any other hosts are present, you can perform a "sweep" of the local network. One means of doing this is to use ```ping``` in a for/next loop. You can also use the ```netdiscover``` tool bundled with Kali.

#### 11. Run the following command to scan the network by using netdiscover. The results should discover several other hosts connected to the vLOCAL switch.
```netdiscover -i eth0 -r 10.1.0.0/24```

The netdiscover may take up to two minutes to complete after it reports as Finished.
#### 12. How many hosts were found by netdiscover?
*The results of the netdiscover listed 5 IP addresses, along with MAC addresses, and some potentially valuable information in the MAC Vendor / Hostname column.*

**Answer: 5**
#### 13. Press q to exit the Netdiscover report and return to the command prompt.
Run netdiscover -h to view the help page. The tool can operate in a passive mode, but you do not need to be stealthy, so you will run an active scan.
#### 14. Switch to the DC1 VM, send Ctrl+Alt+Delete, and then login as 515support\Administrator using Pa$$w0rd as the password.
#### 15. Right-click the Start menu and select Command Prompt (Admin). When prompted, confirm UAC by selecting Yes
#### 16. Run the following command to display the IP address configuration for DC1:
```ipconfig```

*Notice how this differs from the similar command used for Linux, ```ifconfig```, This is a strong indication that this is a Windows device*
#### 17. What is the IP address for the DC1 virtual machine? (answer format: 1.2.3.4)
*Running the command gives a similar output to that of the ```ifconfig``` command in Linux. We can see an IPv4 address displayed.*

**Answer: 1-.1.0.1**
#### 18. Run the following command to test the reliability (packet loss) and latency (delay) of the connection between the DC1 VM and the PT1-Kali VM (the test takes 30-45 seconds to run):
```pathping 10.1.0.192```

#### 19. What percentage of packets were lost during this test? (Reply with values such as 0%, 50%, 100%, etc)
*The results have a column called "Lost/Sent = Pct" and underneath that column is a single entry: 0/ 100 = 0%, so it appears the answer is 0%*

**Answer: 0%**

The two machines are not very far apart on the network—on the same subnet in fact. If you run pathping against network nodes that have several routers between them, the utility displays lost packets along the route, which helps to clarify where network communications might be unreliable. For example, from a Windows computer with Internet access, you could run pathping 8.8.8.8 to trace the route to the servers running Google's DNS service. Note that the lab virtual machines do not have Internet access.


### Use nmap to discover hosts
From a penetration tester or threat actor perspective, network reconnaissance will typically aim to discover the following:
* Default gateway (the router connecting the subnet to other networks).
* DNS server (used to resolve host names on the network).
* Whether any network directory/authentication and application servers are present.
* Whether any host/client access devices are present.
* Whether any other types of devices (embedded systems or appliances) are present.

Use Nmap from the PT1-Kali VM to report this information for this network.

#### 1. Switch back to PT1-Kali. If necessary, log in as root using Pa$$w0rd as the password.

#### 2. Run the following command to scan the Kali VM:
```nmap localhost```
#### 3. What port is open on the virtual machine?
*The results of the nmap scan shows only one port that is open. This is port 22, which is hosting SSH services.*

**Answer: 22/tcp**
#### 4. Run the following command to do a basic network scan:
```nmap 10.1.0.0/24```

*The results of the scan show 6 hosts up with different ports that are open.*

#### 5. Run the following command and check the output. What services are running and what do they tell you about the host?
```nmap -sS 10.1.0.254```

This syntax will scan the default port range (1000 ports) on the target.
#### 6. What services are running on this virtual machine (the router)?
*The results show two ports: port 22, which hosts SSH, and 53, which hosts DNS*

**Answer: SSH and DNS services**

#### 7. Run the following command to identify more about the host:
```nmap -A 10.1.0.254```
#### 8. What operating system is running on the 10.1.0.254 host?
*If you scroll down to "service info" you can see that this is a Linux OS*

**Answer: Linux**
#### 9. Run the following command to scan for open ports 20-200 on the network:
```nmap -p 20-200 10.1.0.0/24```

*The results show us the open ports between 20 and 200 for each device on the subnet*
#### 10. Is port 80 open on MS1? 
*From the nmap results for the MS1 device, we see that port 80 is indeed open, hosting HTTP services*

#### 11. Run the following command to scan for the twenty most common ports:
```nmap --top-ports 20 10.1.0.0/24```
#### 12. Run the following command to quickly scan the network for hosts that are up or down on the network:
```nmap -sn 10.1.0.0/24```
#### 13. Which server is configured as an email server? (Hint: you'll need to look for the default SMTP port number in the "Ports used" field of each host)
*SMTP is the default email server, so a machine with that port open would be a good sign it is our email server. In our nmap results, only the MS1 machine had this port open, making it our likely answer.*

**Answer: MS1**


### Banner grab with curl and Firefox
**Banner grabbing is a way of identifying service versions.** This information helps attackers select potentially vulnerable machines. The information also helps administrators confirm that all services on the network are of a certain version.

Basic tools can be used to grab banners. In this activity, you will use cURL and Firefox.

#### 1. Run the following command in the Terminal to connect to the 10.1.0.1 HTTP server by using cURL:
```curl -s -I 10.1.0.1```
You are disconnected automatically.


#### 2. What web server service and version is used on the target VM?
*Running the command provides several lines of output. The "Server:" line has "Microsoft-IIS/10.0" which is the name of the web server service we are looking for (IIS stands for Internet Information Services)*

**Answer: IIS 10**

You used port scans in the earlier activities to determine that the 10.1.0.1 virtual machine is running a web service. With this cURL command, you have determined the software type and version for the web server service.
#### 3. In Kali, type firefox http://10.1.0.1 and then select Enter.
```firefox http://10.1.0.1```

#### 4. What web server service is used on the target VM?
*At the top of the browser window, we see a header that says "IIS Windows Server - Mozilla Firefox", which leads me to believe this is an Internet Information Services web server.*

**Answer: Internet Information Services**
#### 5. Close the Firefox web browser.


### Query DNS
DNS provides name resolution to internal networks as well as the Internet. DNS is used any time a user or application refers to a host by name. DNS queries name records to find the IP address associated with a hostname or fully qualified domain name. These name records can also reveal information about how a network is configured. In this task, you will gather DNS information by using the dig utility.

#### 1. Run the dig command in the Terminal to perform a reverse lookup on the default gateway.
```dig -x 10.1.0.254```

#### 2. What is the IP address of the DNS server that answers the query?
*Looking towards the bottom of our output, we see a "query" section that has information about the query. A server is listed with an IP address, which intuitively we can assume is the IP of the DNS server, as the DNS server would be the one to answer the dig query. This assumption is further supported, as it is the only IP address listed in the output*

**Answer: 10.1.0.1**
#### 3. Use dig to answer the following question: What is the fully-qualified domain name (FQDN) of the VM found at 10.1.0.2? (Hint: Check the ANSWER SECTION of the output)
*Make sure we can see that this is a different IP than the one from the previous dig command we used, so run the command again, but with the new IP address*

*As we can see, in the answer section, there is a FQDN listed, which is "MS1.corp.515support.com."

**Answer: MS1.corp.515support.com.**

The trailing dot represents the root domain. This is not required by client browsers and other user applications, but must be used correctly within name records and configuration files.
#### 4. Run the following command to display the authoritative DNS server for the namespace:
```dig soa corp.515support.com```

The query returns the FQDN of the DNS server responsible for the domain (DC1.corp.515support.com) and its host record (10.1.0.1). It's also worth noting some of the flags shown:

* aa indicates that the answer is authoritative. The "AUTHORITY" section of the response is empty. Contents for this section are commonly omitted by name servers to reduce the size of responses.

* ra indicates that recursion is available; that is, this router will forward queries to other servers.

#### 5. Close the terminal window.


### Comprehensive Questions

#### 1. Which of the following answers correctly summarizes the steps in this activity?

**Answer: Identified the Kali Linux and Windows servers, performed basic scans of the network, identified hosts and services, used banner grabbing to identify services, gathered more DNS information.**
#### 2. Which of the following answers best identifies how an administrator might use the information gathered during this activity?
**Answer: To verify expected network and server configurations.**
#### 3. Which of the following answers best identifies how an administrator might use the information gathered during this activity?
**Answer: To verify expected network and server configurations.**
#### 4. Which of the following answers best identifies how an attacker might use the information gathered during this activity?
1. **to map the network in preparation for an attack.**
2. **to check for old services that may be unpatched.**
3. **to decide what servers to attack**


We have now successfully completed all the steps to scan and identify network nodes by using reconnaissance techniques such as ping, nmap, ip neighbor, arp, curl and dig. Continue practicing with these commands, and check the -h, --help and man pages to see what switches can be used with these commands. If you have TLDR installed on your machine, you can also run this command to see if there is a TLDR page, which are incredibly helpful. *To run the tldr command, run ```tldr <service name>```*