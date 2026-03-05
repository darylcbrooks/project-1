### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/WCZIFP6mhvA/0.jpg)](https://youtu.be/WCZIFP6mhvA)

### [Project 10 - More packet analysis with Hping](https://youtu.be/WCZIFP6mhvA)

While continuing my exploration with packet analysis, in this project I will be taking a different approach. This time, I will be using the command‑based tool Hping to generate network traffic and Tcpdump to scan various types of packets for comparison and contrast. However, before diving into that, I also wanted to revisit the Nmap tool to demonstrate some additional practical uses for it.

### Saving Nmap Output in Different Formats

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%2010%20-%20Step%2001.png)

In my Kali command terminal, the first thing I need to do to ensure I have the proper privileges to execute all of the commands I’ll be using is to switch to the root account (command used: “sudo su” and my account password). This is usually ill‑advised due to the inherent risks associated with using the root account, but for the sake of demonstration I will be using it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2002.png)

In my previous project walkthrough, I stressed the importance of saving packet captures for further analysis and demonstrated how to do so. The same logic applies to Nmap scans, and I want to show the various format options available when storing them. In the above image, I run an Nmap scan against a target host (192.168.1.101) and include the “-oN” parameter. This tells Nmap to scan the host and save the output in “normal” format to a document named “normal_output” (nmap 192.168.1.101 -oN normal_output). The “normal” format saves the scanning output exactly as it appears on the screen. To verify this, I run the “cat” command with the “normal_output” document name, and we can see that what is displayed matches Nmap’s initial output (cat normal_output).

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2003.png)

The second format option at my disposal is XML (Extensible Markup Language). XML is easily parsed by programming languages and is commonly used when the output needs to be machine‑readable. For example, if I wanted to read the output of an Nmap scan using a tool like the Metasploit Framework, the XML format would be ideal since it’s meant to be interpreted by software rather than a human. In the example above, I run an Nmap scan against the same target host but this time add the “-oX” parameter. This tells Nmap to save the scan output in XML format to a document named “xml_format.xml” (nmap 192.168.1.101 -oX xml_format.xml). When I run “cat xml_format.xml,” the contents are not human‑readable but are readable to any system or software capable of parsing XML.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2004.png)

The final option I can use to save Nmap output is the grepable format. A grepable format is a specialized text structure designed for easy parsing by command‑line tools such as “grep,” “awk,” “sed,” and “cut.” It lists each host’s information (IP, status, port details) on a single consistent line, making it ideal for filtering network scan results or performing log analysis without writing complex scripts. To output Nmap results in this format, the “-oG” parameter must be added to the command along with the file name to save the results to. In the above example, I run another Nmap scan against the same target host and tell Nmap to save the output in a grepable format to a file named “grepable_format” (nmap 192.168.1.101 -oG grepable_format). We can see that compared to the “normal” format, all the same details are captured but in two rows, ordered numerically from least to greatest.

### Nmap SYN and Non-ICMP Scans

Before moving further with my Nmap scans, I want to switch to a more restricted user account to demonstrate why elevated privileges are sometimes required. Instead of switching back to the normal Kali user account, I’m taking this opportunity to also demonstrate how to create a new user on Kali Linux.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2005.png)

To create a new user, I use the “useradd” command along with the name of the account I want to create, “user1” (useradd user1). Now that this unprivileged account has been created, I use the “su” command along with “user1” to switch to that user (su user1). Since I haven’t set a password yet, none is required and I’m automatically logged in as “user1.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2006.png)

I want to start by attempting to run an Nmap SYN scan on our target host. A SYN scan is when Nmap sends only the first part of the TCP three‑way handshake (SYN, SYN‑ACK, ACK) — specifically, a SYN packet — to enumerate port details in a stealthier manner (nmap -sS 192.168.1.101). In the image, we can see that this did not work, and Kali returned a message informing me that root privileges are required to perform this type of scan. Now let’s try again using the root account.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2007.png)

I use “sudo su,” which switches me back to the root account after providing my password. From there, I run the same scan again (nmap -sS 192.168.1.101), and this time Nmap successfully returns output. This demonstrates the importance of elevated privileges for certain commands and why intruders often attempt privilege escalation after compromising a system.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2008.png)

Before moving on to Hping, there is one last Nmap scan type I want to cover. Normally, when Nmap begins scanning, it sends an ICMP packet to the target host to check if it’s online. ICMP packets are what allow us to ping devices running on a network. This can be problematic for an ethical hacker if stealth is required, as several security controls (IDS, IPS, SIEM tools, etc.) can detect and log ICMP activity. To avoid this, there is a way to run an Nmap scan without pinging the target. By adding the “-Pn” parameter, Nmap skips sending ICMP packets altogether. The example above shows this in action, where I run Nmap with the “-Pn” parameter and still receive output regarding the open ports on the target host (nmap -Pn 192.168.1.101).

### Generating Network Traffic w/ Hping

Hping is a command‑line utility for assembling and analyzing TCP/IP packets. This packet‑crafting tool supports various protocols including TCP, UDP, and ICMP, and allows manipulation of multiple values in a TCP/IP header such as window size, packet size, and time‑to‑live (TTL). Among its many uses, Hping can be used for: 

• Firewall testing
• Advanced port scanning
• Network testing using different protocols, TOS, and fragmentation
• Manual Path MTU discovery
• Advanced traceroute across all supported protocols
• Remote OS fingerprinting
• Remote uptime estimation
• TCP/IP stack auditing

The latest version, Hping3, is scriptable, enabling penetration testers and programmers to write scripts for modifying and analyzing TCP/IP packets.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2009.png)

Starting Hping only requires the user to type and execute the version of Hping installed on the system. I’m using Hping version 3, so I simply type “hping3” to start the tool. Since this was just an example, I type “exit” to return to the Kali CLI.

## SYN Packet Generation and Port Scanning w/ Hping

I’ve already demonstrated how to conduct a SYN scan with Nmap, so now let’s see how it’s done using Hping.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2010.png)

The following command runs a SYN scan on the HTTP port (port 80) of the target host by sending a single SYN packet: hping3 -S 192.168.1.101 -p 80 -c 1.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2011.png)

Next, I use Tcpdump to begin capturing any generated HTTP traffic on interface eth0 and save the output to a file named “syn_scan” (tcpdump -i eth0 port 80 -nx > syn_scan).

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2012.png)

I open a new command terminal and run “hping3 -S 192.168.1.101 -p 80 -c 1” again (this time using “sudo” for elevated privileges) to generate the HTTP traffic Tcpdump is listening for.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2013.png)

Back in the terminal running Tcpdump, I press Ctrl + C to stop the packet capture. Then I use the “cat” command to view the results (cat syn_scan). On the first line under “cat syn_scan,” we see that a packet with the “S Flag” was captured — the SYN packet that Hping sent. A few lines below, another captured packet contains the “R Flag.” This is a RST packet, sent back because the TCP handshake was never completed. This makes sense since we only sent a SYN packet and were not expecting an ACK in return.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2014.png)

Hping can also scan multiple ports at once. The “-8” parameter in the following command tells Hping to scan TCP ports 0–100: hping3 -8 0–100 -S 192.168.1.101. The output shows the ports and protocols in use and the types of packets sent and received. Unlike the previous single‑port SYN scan, this one returns “A Flags” (ACK packets), indicating which ports responded. The TCP handshake still doesn’t complete, as we are not acknowledging the responses.

## ACK Packet Generation w/ Hping

I can also use ACK packets to check whether a host or its ports are active. This starts in the second phase of the TCP three‑way handshake, and the target should send an acknowledgment packet in response.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2015.png)

I again use Tcpdump, this time listening for SSH traffic on port 22 and saving the output to “ack_scan”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2016.png)

In the second terminal, I use the following command to send an ACK packet to port 22 of the target host: sudo hping3 -A 192.168.1.101 -p 22 -c 1.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2017.png)

Returning to the Tcpdump terminal, I press Ctrl + C to stop the capture and then view the results (cat ack_scan). On the first line, we see a packet represented by a simple “.” with “ack” shown next to it in lowercase, indicating that the ACK packet was successfully sent and captured. A few lines down, another packet contains an “R Flag.” Technically, an ACK should have been returned, but the absence of one is likely due to the first step of the TCP handshake never occurring.

## UDP Packet Generation w/ Hping

UDP ports (connectionless protocols) can also be scanned with Hping.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2018.png)

I begin the same way as before by having Tcpdump listen for traffic on UDP port 161 and save the output to “udp_scan” (tcpdump -i eth0 port 161 -nx > udp_scan). UDP port 161 is the default SNMP port. The Simple Network Management Protocol is an application‑layer UDP protocol used to manage and monitor network devices remotely.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2019.png)

In another terminal, I generate SNMP traffic with: sudo hping3 -2 192.168.1.101 -p 161 -c 1. Notice that the command does not specify a packet type. This is because, unlike TCP, which uses the three‑way handshake, UDP is connectionless.

![Image](https://github.com/darylcbrooks/project-1/blob/project-10/Project%210%20-%20Step%2020.png)

Back in the Tcpdump terminal, I press Ctrl + C and view the output (cat udp_scan). At the end of the first line under “cat udp_scan,” there is a message stating that there’s nothing to parse.

### Conclusion

Using Hping in conjunction with Tcpdump provided great insight into how the TCP three‑way handshake can be used in various ways to detect running hosts and open ports. This project also demonstrated the various formats Nmap scans can be saved in and highlighted the importance of user privileges when running certain scans. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
