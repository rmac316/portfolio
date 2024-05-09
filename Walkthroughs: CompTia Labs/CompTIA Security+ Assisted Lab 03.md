# CompTIA Security+ Labs
## Assisted Lab 3: Intercepting and Interpreting Network Traffic with Packet Sniffing Tools
**Scenario: In this activity, you will use Wireshark and tcpdump to capture network traffic and display relevant information on the local network. Interpreting the output from such captures is useful for security assessments.**

### Sniff network traffic
Use the Kali Linux virtual machine to capture some network traffic and identify the main features of the Wireshark network analyzer. You'll assume that KALI has been able to obtain some sort of network tap, which has been simulated for you already by configuring port mirroring on the Hyper-V switch.

#### 1. Select the PT1-Kali and log on with the credentials root and Pa$$w0rd

#### 2. From the desktop, select the Wireshark application.
Maximize the window to make Wireshark easier to work with.

#### 3. Under Capture, select the eth0 adapter.

#### 4. In the Capture filter box, type ip.
*The ip filter captures IPv4 traffic, not IPv6 traffic.*

There are two types of filters: capture restricts which frames the sniffer records while display filters (but does not discard) what has been recorded. The syntax of capture and display filters is different, and capture filters are more basic.

#### 5. Start the capture by selecting the blue Start capturing packets button in the upper left corner of the Wireshark interface.

#### 6. On the PT1-Kali VM, launch Firefox from the menu, and then connect to http://10.1.0.2 (the MS1 Windows web server).

#### 7. As soon as the 515support web page is displayed in Firefox, switch back to the Wireshark application.
In this task, you are capturing packets on the PT1-Kali VM and connecting to the MS1 web server from the same PT1-Kali VM.

*Capturing traffic on a client computer as it is attempting to connect to a destination computer might be useful in both security and network troubleshooting scenarios.*

#### 8. Select the Stop button on the toolbar to end the live capture.

#### 9. Select any DNS frame (they are color-coded as light blue) from the top panel, and then observe the frame contents displayed in the middle panel.
Wireshark splits out the successive headers and payloads to decode each protocol:
* Frame—this shows information about the bytes captured.
* Ethernet II—this shows the frame type (data link layer/layer 2) and the source and destination MAC addresses. Note that the first part of the address (the OUI) is identified as belonging to Microsoft (all the VMs are using MS virtual adapters). The last piece of information is the type of network protocol contained in the frame (IPv4).
* Internet Protocol Version 4—this is the IPv4 datagram, notably showing the source and destination IP (layer 3) addresses. Note there is usually also a GeoIP function in this section, but as these are private addresses, they cannot be resolved to a particular regional registry or ISP.
* User Datagram Protocol—layer 4 (transport) uses either UDP or TCP. The most significant fields here are the source and destination ports. UDP port 53 is the "well known" DNS server port.
* Domain Name System—this is the application protocol. Depending on which frame you selected, you may be looking at a query or at a response.

*Observe how this window in Wireshark reflects the layers of the TCP/IP stack (though it is inverted). Ethernet II displays physical (MAC) address information, Layer 3 shows logical addressing (IP) and layer 4 shows application layer source and destination ports. The application layer contains protocol-specific headers and the data payload (DNS in this example).*

#### 10. Select the TCP frame that opens the connection to the web server (the first frame that is colored green), and then examine the contents of the middle panel.
Be sure to note both the source and destination port numbers.

Use the Wireshark interface to answer the following questions.

#### 11. In the Wireshark interface, what is the name of the layer where the MAC addresses are found (Hint: this info is in the middle pane of the interface)?
*As we can see in the frame in Wireshark, there are MAC addresses listed next to the Ethernet II header, so this appears to be our answer.*

**Answer: Ethernet II**

#### 12. In the Wireshark interface, what is the name of the layer where the IP addresses are found (Hint: this info is in the middle pane of the interface)?
*Next to the Internet Protocol Version 4 header, there are IPv4 IP addresses for the source and destination devices.*

**Answer: Internet Protocol Version 4**

#### 13. In the Wireshark interface, what is the name of the layer where the source and destination port numbers are found (Hint: this info is in the middle pane of the interface)?
*Again, looking at the headers in the middle frame of Wireshark, we see a source and destination port in the Transmission Control Protocol.*

**Answer: Transmission Control Protocol**


### Use tcpdump to intercept HTTP network traffic
The tcpdump program is another protocol analyzer (packet sniffer). In this activity, you will use tcpdump to intercept HTTP traffic.

#### 1. Switch to the MS1 VM, select Ctrl+Alt+Delete, and then sign in as 515support\Administrator with Pa$$w0rd.
* The MS1 VM is hosting a web site that will be used as part of the packet interception activity.

#### 2. From Server Manager, select Tools > Internet Information Services (IIS) Manager.

#### 3. Expand the MS1 and Sites nodes to display the Default Web Site node.

#### 4. Double-click the Authentication applet in the Default Web Site Home pane.

#### 5. Select Anonymous Authentication, and then from the Actions pane on the right, select Disable.

#### 6. Select Basic Authentication, and then from the Actions pane on the right, select Enable.
* Note the alert about the lack of SSL and cleartext credentials, but ignore it for the purposes of this activity.

#### 7. Switch to the PT1-Kali VM, and then confirm that you are signed in. If necessary, log on with the credentials root and Pa$$w0rd.

#### 8. From the menu at the top of the desktop, open the Terminal.

#### 9. Run the following command to begin intercepting MS1 HTTP network traffic by using tcpdump:
```tcpdump -vv dst 10.1.0.2 and port www -w /root/www.pcap```

* You will not see captured frames in the Terminal output. The -w switch directs output to a capture file.

#### 10. From the top menu, start Firefox, and then connect to the web site hosted at http://10.1.0.2.

#### 11. You will be prompted to enter the following name and password (if not, press F5 to refresh the page):
515support\Administrator
Pa$$w0rd

#### 12. Switch to the Terminal window and then select Ctrl+C to stop the tcpdump capture.

#### 13. Switch to the Wireshark application. If you closed the program, launch it again from the desktop. Select the File menu, and then select Open.

#### 14. Select the www.pcap file from the root user's home directory, and then select Open. If prompted, select Continue without saving to discard the previous Wireshark scan and open this file.
* tcpdump files are not stored in a human-readable format. Applications such as Wireshark can be used to display their contents.

#### 15. Select one of the GET HTTP messages from the top panel in Wireshark. With the GET message selected, in the middle panel, expand Hypertext Transfer Protocol, and then expand Authorization.
* If you had to refresh the page, select one of the GET frames in the second block of HTTP requests to see the authorization header.

#### 16. Which of the following options shows how the authentication information is displayed?
*This is important for security as well as pentesting; as a pentester, finding credentials such as an admin's username and password would be invaluable, so this is an important tool to add to your enumeration skillset.*

**Answer: Credentials: 515support\Administrator:Pa$$w0rd**

* HTTP packets are unencrypted and therefore vulnerable to packet sniffing.

### Use tcpdump to intercept SSH network traffic
You will use tcpdump to intercept SSH network traffic and attempt to display contents of the traffic. You will use an SSH connection to the 10.1.0.10 IP address. This address is assigned to the LX1 virtual machine, which runs the CentOS 7 Linux distribution.

#### 1. On the PT1-Kali VM, run the following command to begin intercepting SSH network traffic by using tcpdump:
```tcpdump -vv dst 10.1.0.10 and port ssh -w /root/ssh.pcap```

#### 2. Open a second Terminal window and then run the following command:
```ssh root@10.1.0.10```

#### 3. Enter yes to confirm the connection attempt, and then enter Pa$$w0rd as the password.

#### 4. Run the hostname and whoami commands to confirm you are the root user on the LX1 remote system.

#### 5. Run the exit command to disconnect from the remote system.

Switch to the terminal window hosting tcpdump and then press Ctrl+C to stop the capture.

Switch to the Wireshark application. If you closed the program, launch it again from the desktop.

Select the File menu, and then open the ssh.pcap file from the root user's home directory.

This is the same task you did above with the www.pcap file.

Spend a few minutes browsing the captured SSH information.

Specifically, do you find any authentication credentials? Also notice details such as the source and destination port numbers, etc.

#### 10. Were you able to read information about the root user, such as the password?
**Answer: no**

### Comprehensive questions
Answer the following final comprehensive questions to ensure that you recognize the importance of the activity steps and the uses for the information you have learned.


#### Which of the following answers best describes the content you viewed by using the tcpdump utility?
*Thinking back, we used tcpdump and Wireshark to look at frames from connecting to a web page from the browser. Web pages use HTTP and HTTPS. Since we were able to see the plaintext credentials of the administrator (we also specifically entered the web address as http, not https), we can assume it is HTTP. We then tried to intercept an SSH interaction between two devices. While we were able to observe much of the exchange in Wireshark, we were unable to extract any credentials, as SSH has encrypted communication.*

**Answer: HTTP and SSH**


#### Would an eavesdropper be able to intercept and read the contents of the web page on the MS1 (10.1.0.2) virtual machine if the web server required HTTPS connections?

**Answer: No, the contents would have been encrypted and therefore unreadable.**
