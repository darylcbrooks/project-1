### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/MkJYiIh7fLk/maxresdefault.jpg)](https://youtu.be/MkJYiIh7fLk)

### [Project 08 - Packet Analysis pt 2: Network Traffic Monitoring with Wireshark & Tcpdump ](https://youtu.be/MkJYiIh7fLk)

Welcome to the walkthrough of my second packet analysis project. During my first project on this subject matter, I demonstrated how the command-line–based packet analysis tool TShark could be used to capture and analyze network traffic for secure and insecure protocols (Walkthrough: Packet Analysis with TShark: Telnet vs. SSH 📡 | by Daryl Brooks | Feb 2026 | Medium). In this project, I’m going to demonstrate something similar, but this time using the tcpdump utility and TShark’s GUI-based cousin, Wireshark, to capture and analyze network traffic. Let’s get started:

### Capturing Telnet Traffic w/ Wireshark

Wireshark is the most popular free, open-source network protocol analyzer used to capture and interactively browse traffic on computer networks in real time. It acts as a “network x‑ray,” allowing users to troubleshoot network problems, analyze security issues, and inspect hundreds of protocols.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2002.png)

To boot up Wireshark from the Kali CLI, I type and execute the sudo wireshark command and provide my account password. This grants the root-level privileges needed to launch the Wireshark GUI. From here, I begin capturing network traffic on the eth0 network interface (since it is the one in use) and click the blue fin icon in the upper-left corner of the window.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2003.png)

Now I need to generate traffic for Wireshark to capture. To do so, I open a new command terminal in Kali and log into another host via the Telnet protocol (sudo telnet 192.168.1.102). After authenticating with the necessary credentials (username: root, password: toor), it appears that I’ve successfully established a Telnet session with the other host. I verify this by using the whoami command, which returns the user account I’m logged into "root."

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2004.png)

This simple authentication should have generated enough traffic for analysis in Wireshark, so I type the exit command to end my Telnet session.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2005.png)

Back in Wireshark, I locate and right‑click a Telnet packet (it should be clearly labeled “Telnet” under the Protocol column), choose the “Follow” option, and select “TCP Stream.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2006.png)

Following the TCP stream associated with this Telnet packet allows me to see the data contained within it. Because Telnet is a cleartext protocol, much of what I see is readable English. Without having to look far, the highlighted portion of the image shows both the username (root) and password (toor) that were transmitted during authentication. Now imagine if a malicious actor had this same capability — they would have the means to authenticate into an internal system and potentially access or manipulate sensitive information, or even begin lateral movement within the network. This is why it is vitally important not only to avoid insecure protocols like Telnet, but also to disable them by default to harden your network infrastructure.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2007.png)

Having found what I needed, I stop the packet capture by clicking the stop button (the red square) at the top of the window.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2008.png)

As a security analyst, it’s always best practice to save any potential security findings from a packet capture session for further analysis. To do so, I go to the “File” menu at the top of the window and choose the “Save As…” option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2009.png)

The format should be set to either .pcap or .pcapng, which are the default file formats Wireshark reads. I decided to name the file telnet_packets to reflect its content.

### Capturing SSH Traffic w/ TcpDump

What is tcpdump? It’s a command-line packet analyzer and network monitoring tool similar to TShark and Wireshark, used to capture, log, and inspect real-time network traffic.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2001.png)

Typing and executing tcpdump --help in the Linux terminal brings up a menu that helps me familiarize myself with the various parameters available in tcpdump.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2010.png)

Back in the terminal used earlier for my Telnet session, I type and execute the following command to have tcpdump begin capturing SSH traffic and write the output to a file named ssh_packets.pcapng:
sudo tcpdump -i eth0 -w ssh_packets.pcapng

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2011.png)

With tcpdump now ready to capture network traffic on TCP port 22 (SSH’s default port), I must generate packets for it to capture. Similar to before, I open a new command terminal and establish an SSH session with another host by authenticating to it (ssh root@192.168.1.100, password: passw0rd!). Upon successful login, I verify it by using the whoami command, which confirms that I am logged in as “root.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2012.png)

Back in the terminal running tcpdump, I press Ctrl + C to stop the packet capture. I can immediately see that tcpdump captured 112 packets during the session. The details of these packets are saved in ssh_packets.pcapng.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2013.png)

I can view this by importing the file into Wireshark and following the packet stream in its GUI. I return to Wireshark, open the “File” menu, select “Open,” and choose the ssh_packets.pcapng file saved in my local directory.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2014.png)

Now I can see the full extent of the 112 packets captured by tcpdump. However, I only care about those related to the SSH protocol. To filter for relevant packets, I simply type ssh into the filter textbox below the Start and Stop buttons. With only SSH packets displayed, I right‑click one and choose the necessary options to follow the packet stream.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2015.png)

When following the SSH packet stream, aside from references to the encryption algorithms in use, there is no human‑readable text. This clearly demonstrates how encrypted protocols like SSH are safer than cleartext ones like Telnet. Even if a malicious actor were to intercept my network traffic, they would not be able to read the contents of the packets. To save this packet stream only, I click “Save As…” at the bottom of the window and name the file key_exchange.pcapng.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2016.png)

### Capturing FTP Traffic w/ Wireshark

Now I want to capture and compare encrypted and unencrypted FTP traffic. To begin capturing unencrypted traffic, I click the blue fin (Start) button in the upper-left corner of the Wireshark window. Back in the terminal where I authenticated to another host using SSH, I type exit to terminate the connection. Next, I create a new file called ftp_test.txt and save the word “unencrypted” inside it using the command:
echo "unencrypted" > ftp_test.txt

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2017.png)

To generate FTP traffic, I establish an FTP connection with another host and upload the file I just created. The command ftp 192.168.1.103 enables me to authenticate to the host using known credentials (username: user, password: password).

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2018.png)

After receiving the 230 return code, which verifies successful authentication, I upload the file using:
mput ftp_test.txt

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2019.png)

This generates sufficient traffic in Wireshark, so I end the FTP session by typing exit.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2020.png)

Returning to Wireshark, I can see the packets generated by my FTP activity. I click the Stop button at the top of the window to halt the capture and apply the following filter to see only traffic going to the host I connected to (192.168.1.103):
ip.addr == 192.168.1.103

Once filtered, I right‑click the first FTP packet and follow the TCP stream.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2021.png)

Similar to what I saw with the Telnet stream, the cleartext credentials are immediately visible (USER user, PASS password). The same logic for avoiding and disabling insecure protocols applies here just as it did with Telnet.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2022.png)

Before moving on, I save the FTP packet capture as ftp_packets.pcapng in my home directory. Now let’s see what encrypted FTP traffic looks like.

### Capturing SFTP Traffic w/ Wireshark

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2023.png)

Before starting a new capture in Wireshark, I enable a filter to display only SSH traffic, so I won’t need to filter later:
tcp.port == 22

With the filter set, I click the blue fin (Start) button to begin capturing. My goal is to generate SFTP traffic and compare it to unencrypted FTP traffic. The command sftp user@192.168.1.104 establishes an SFTP connection over SSH with another host. After providing my password (password), I’m successfully authenticated.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2024.png)

As before, I create a file called sftp_test.txt and store the word “unencrypted” inside it (echo "unencrypted" > sftp_test.txt).

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2025.png)

After creating the file, I upload it using:
put sftp_test.txt /upload

Having generated the needed traffic, I return to Wireshark to view the results.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2026.png)

Wireshark shows only SSH packets, as expected. I also try the filter ip.addr == 192.168.1.104 to see if it produces the same results—and it does. I right‑click the first SSH packet and follow the stream.

![Image](https://github.com/darylcbrooks/project-1/blob/project-8/Project%208%20-%20Step%2027.png)

As expected, there is no cleartext output. No one, including myself, can see the credentials used for SFTP authentication. This demonstrates why SFTP is a more secure alternative to its unencrypted counterpart.

To finish things off, I save this packet capture as sftp_packets.pcapng in my home directory.

### Conclusion

In this project, I used both the command-line–based tcpdump tool and the GUI-based Wireshark to demonstrate the differences between encrypted and unencrypted network traffic. Encrypted protocols such as SSH provide transport‑layer protection to data in transit and ensure the confidentiality of transmitted packets. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
