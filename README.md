### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/liKpmWmCzR8/0.jpg)](https://youtu.be/liKpmWmCzR8)

### [ Project 18 - Detecting Port Scans w/ Wireshark](https://youtu.be/liKpmWmCzR8)

In this project, I’ll be demonstrating how a packet analysis tool such as Wireshark can be used to detect port scanning. Port scanning is typically part of the initial active reconnaissance phase of a cyber‑attack. The earlier this activity is detected, the higher the probability that the attack can be thwarted before any serious damage is done. I will be playing two roles in this project: a red team attacker and a blue team defender.

## Blue Team: Opening Wireshark for Network Traffic Monitoring

![Image](https://github.com/darylcbrooks/project-1/blob/project-18/Project%2018%20-%20Step%2001.png)

Starting off as the blue team analyst, I click the Wireshark icon on my Windows* desktop to open the application.

*Note: Windows Server 2012 is the operating system in use.

![Image](https://github.com/darylcbrooks/project-1/blob/project-18/Project%2018%20-%20Step%2002.png)

Once the Wireshark GUI loads, I click the blue fin icon in the upper‑left corner of the window to begin monitoring traffic on my network. Now let’s generate some traffic to capture.

## Red Team: Generating Network Traffic w/ an Nmap Port Scan

Let’s pretend that as the red team attacker, I’ve already discovered the devices that are currently active on the targeted network and have narrowed my focus to the device with an IP address of 172.16.0.15. One way this discovery can be performed is by conducting a ping sweep of a network. A ping sweep sends ICMP packets to ping all, or a specified range, of devices within a network and determines which ones are active based on the devices that respond (e.g., fping -a -g 172.16.0.0/24).

![Image](https://github.com/darylcbrooks/project-1/blob/project-18/Project%2018%20-%20Step%2003.png)

Now that I’ve selected a host to target, I run an Nmap scan against it to begin enumerating open ports and the services running on them (nmap 172.16.0.15).

![Image](https://github.com/darylcbrooks/project-1/blob/project-18/Project%2018%20-%20Step%2004.png)

This will generate the traffic necessary for the blue team analyst to begin their investigation.

## Blue Team: Reviewing Port Scan Traffic

![Image](https://github.com/darylcbrooks/project-1/blob/project-18/Project%2018%20-%20Step%2005.png)

Back on the Windows machine, a large number of packets appear as a result of the recent port scan. However, as an analyst, I only want to see those that would be indicative of port‑scanning behavior. To do this, I apply a filter that displays only RPC‑related traffic (tcp.port == 135). The Remote Procedure Call (RPC) protocol helps devices discover services on a network and their assigned ports. By default, it operates on TCP port 135. Given the nature of an Nmap port scan, there is no doubt that if one was performed, RPC traffic would be present.

The results confirm this by displaying three packets, each showing a different phase of an incomplete TCP three‑way handshake.

There are a few things to remember:

* For a TCP connection to be successful, three packets must be exchanged between the devices (SYN, SYN‑ACK, ACK). This is known as the TCP Three‑Way Handshake.

* When Nmap conducts a TCP port scan, it sends a SYN packet by default. If the port being scanned is open, the target machine responds with a SYN‑ACK packet. The third step of the handshake never completes because Nmap is not attempting to establish a connection — only to elicit a response.

Given these two facts, we can see the SYN packet sent from 172.16.0.5 (the red team machine) to 172.16.0.15 (the target host). The second packet shows the SYN‑ACK returned from the target host to the red team machine. Lastly, the third packet is an RST (reset) packet, which is generated when a TCP handshake fails to complete. As a blue team analyst, this would be an excellent starting point for further investigation into address 172.16.0.5 and the cause of the incomplete TCP handshake.

## Conclusion

The evidence found in Wireshark isn’t enough by itself to flag a potential breach, but it is behavior worth investigating. It would draw the type of attention that the user at address 172.16.0.5 would likely prefer to avoid — and may stop or at least slow down any intended malicious activity. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
