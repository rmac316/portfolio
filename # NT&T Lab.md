# NT&T Lab

## Overview

Two-Charlie (2C) was assigned the task of building a small business environment for a client. Parameters for the business environment were to include:

- A secure network
- An internal Windows Domain
- An internal IIS webserver (Microsoft)
- An internal Windows 10 workstation
- A public webserver
- A public FTP server
- LAN, DMZ, and Guest networks with their own subnet masks of /24.

The construction of the network environment was completed in six stages (plus Stage Zero): Network Setup; Domain Setup; IIS Setup; LAMP Setup; FTP Setup; and Hardening the Environment.


### Stage 0: Starting From Zero

Stage Zero did not involve the creation of any new devices but was instead used to discuss the parameters of the network environment that has been requested by the client. The secure network would require three subnets that each hosted various services. The starting topology was provided, which included the WAN Cloud and a connected WAN switch. This was promulgated among the group, and the team set out to build the network environment.

### Stage 1: Network Setup

Stage 1 involved the beginning stages of building the network infrastructure. To begin this process, 2C followed instructions and visual recommendations for topology provided by the Senior Engineer (SE) to configure a LAN network.

Network setup was conducted in the GNS3 virtual environment. This began with adding a FortiGate firewall to the WAN switch on eth0. When turned on and activated, a PuTTY interface pops up to prompt login. An administrator account and password were created on the firewall. From there, 2C began configuring the LAN network.

The LAN Network configuration process included:
- Adding a LAN switch to port 2 of the FortiGate firewall.
- From the FortiGate firewall, configured the LAN interface at port 2 to set allowances and IP address.
- Following port 2 configuration, configured DHCP server for the LAN interface, setting DHCP pool scope to 10.128.0.[100-199].
- Adding Windows 10 workstation to LAN network.
- After adding workstation, 2C pinged remote destinations to test WAN, LAN, and DNS. As WAN and DNS had mot been created at this point, these two ping tests failed as expected. 
- Connecting to the FortiGate GUI from the Windows 10 workstation to:
   * backup the confugration
   * configure network interfaces
   * configure network DNS
   * create service objects and policies



All steps and processes performed as expected.

**The importance of this state is the establishment of a LAN network and configure the firewall to allow devices to communicate**


### Stage 2: Domain Setup

The purpose of this stage was to create a domain for the network. Per client request, the domain name chosen for this network was "widgets.localdomain", which was built on a Windows Server 2012r2.

To begin this stage, a Windows Server 2012r2 was added to the virtual network environment in the LAN network and connected to the LAN switch. Active Directory (AD) services were installed on the server. From here, new AD user accounts were added to the Active Directory. These included user and administrator accounts.

After creating user accounts, the Windows Server 2012r2 was promoted to Domain Controller (DC), and the Windows 10 workstation was was joined to the domain. This was completed by changing the primary DNS server to the DC IP, syncing the NTP with dc.widgets.localdomain, and join the widgets.localdomain domain. Group policy was then used to change the background on the Windows 10 workstation to ensure the workstation had been successfully joined to the domain. 

All steps and processes performed as expected. 

**The importance of this stage was establishing a domain for the network and promoting our server to Domain Controller. The DC manages IP address pools, group policies, and allows domain member login across domain devices. 


### Stage 3: IIS Setup

The purpose of this stage was to build an IIS webserver for the LAN network, and then add that server to the domain.

A static IP address was manually configured for the server.

The IIS role was assigned to this server through the Server Manager, using a Microsoft guide provided by the SE.

To ensure access, the address of http://localhost/ (with "localhost" being replaced with the IP address of the IIS server) was added to the list of trusted sites in the Internet Options security settings.

To test efficacy of the webserver, 2C created a test web page by creating a test message on Notepad and saving the file to the Documents folder as "test.html". 2C then copied the file from the Documents folder to c:\inetpub\wwwroot\test.html. Upon entering http://localhost/test.html into the browser, the page successfully loaded, and the validation was completed.

All steps adn processes performed as expected.

**The importance of this stage was to establish the IIS Server for the internal LAN network. This allows for internal users to access webpages on the LAN that may be inaccessible to those outside this subnet.**


### Stage 4: LAMP Setup

The purpose of Stage 4 was to create a LAMP (Linux, Apache, MySQL, PHP) webserver on an Ubuntu server. As this is an internet-facing server, and will be accessible by the public, it is placed on the DMZ network, which allows for segmentation from the LAN.

To begin, the group added an Ubuntu server that was linked to the DMZ switch. The following steps were conducted to configure the server:

- Static IP address was manually assigned.
- Hosts file was edited to have:  
    * 127.0.0.1 localhost.localdomain localhost
    * 10.128.10.80 www.widgets.localdomain www
- Set hostname
- Updated packages
- Installed DokuWiki
- Configured the file for the Wiki
- Created a host(A) record in the DNS on the DC

*This is the point where the first configuration issue presented itself. Upon entering the URL to access the DokuWiki installer, the page failed to load, providing a 500 error message. After stopping and restarting all devices, the error message persisted. A full audit of steps taken in this stage appeared to confirm correct steps taken,however the error continued to persist. After internal discussion between team members, 2C reached out to a third-party group, who helped identify the improperly-formatted wiki configuration file.*

*The original formatting was completed in the nano text editor. Nano is not color-coded, and all text appears relatively uniform. At the recommendation of the third-party group, Vim text editor was used to open the configuration file. Vim has color-coded input, which immediately helped identify inconsistent spacing in the configuration file. A corrected format was implemented and attemps to establish connection to the webpage were successful.*

- Once on the DokuWiki Installer webpage, configured DokuWiki.
- Log into Wiki page and add notes.
- Create host (A) files and FQDNs for firewall and IIS server. 
- Set up Virtual IP for the public side of the webserver on the firewall.

**The importance of this stage was creating a publicly accessible webserver on the DMZ network. Since the webserver can be accessed by anyone on the outside, it is imperative to place this on a separate subnet than the LAN, where sensitive data may be stored.**


### Stage 5: FTP Setup

The purpose of this stage was to create a server that hosted an FTP service on the DMZ network. FTP is often critical for business to be able to transfer files between devices. This stage proved to be the most challenging to implement. A setup guide for the FTP server was provided by the SE.

The steps to complete this stage included:

- Adding a Windows 2012r2 server to the DMZ network.
- Configuring server with static IP address, sync NTP to the DC, and join the widgets domain.
- Installing the FTP service

*This is where we encountered perhaps our most challenging obstacle in this project. The provided guide was translated, and proved difficult to interpret. After consensus, 2C searched online for an alternate guide to assist in installing the FTP service.*

*The steps in the new guide were followed step-by-step, a test landing page was created to test connection and attempts to test the setup failed. After all machines were stopped and restarted, and the error continued to persist, 2C once again conducted a full audit on steps. After confirming the appropriate actions were taken for each step, 2C again coordinated with an outside group to discuss the persistent issue.*

*It was realized that the guides found by each group differed, so a member of each party went over each individual step in the respective guides. It became apparent that the guide found by 2C omitted a section regarding Inbound Firewall Rules that was present in the other guide. After consensus was reached, 2C destroyed the existing FTP server and began setup with the guide provided by the third-party group. After following each step provided in the guide, the connection test proved successful.*

**The importance of this stage was to establish an FTP server for the network. FTP is a critical function for most business' infrastructure, and is important for the transmission of documents between the business and its clients, or for internal file exchange.**


### Stage 6: Hardening the Environment

The purpose of this research-intensive stage was add research notes to the DokuWiki page regarding information on hardening the various devices created for this network. This included:

- Hardening a FortiGate firewall research.
- Hardening Windows 10 research.
- Hardening Windows 2012r2 servers research.
- Hardening Ubuntu server 18.04 research.

The research was compiled into the DokuWiki and formatted for easy reference. 

**The importance of this stage was to create a location for the client to readily access strategies for improving network and device security.**


## Summary

2C was tasked with creating a small business network environment for the client. This was accomplished by implementing a firewall, three switches and four servers across three secure subnets. A LAN network with a workstation, a domain controller, and an IIS webserver were placed on one subnet, while a LAMP and FTP server were set up in the DMZ subnet. No devices were placed on the Guest network, however the network was configured during the construction of the network environment.

## Lessons Learned/Areas of Improvement

A significant amount of time was spent trying to troubleshoot the perceived technical issue or failure to adhere to guide instructions. Future recommendations include the team focusing on troubleshooting the quickest fixes first, rather than begin by attempting in-depth technical audits. 

Additionally, it was noted that there was a bit of a lack of engagement from other group memebers at points. For the sake of time constraints, too much focus may have been placed on completion of the task rather than collaboration between group members. It is recommended that future collaborative efforts have heavier focus on team engagement. 
