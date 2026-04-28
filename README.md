### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/R9HTliPGPcE/0.jpg)](https://youtu.be/R9HTliPGPcE)

### [Project 33 - Using Wireshark Filters to Detect Indicators of Compromise](https://youtu.be/R9HTliPGPcE)

This project will focus on how indicators of compromise can be discovered when viewing network traffic captures in Wireshark and applying certain filters to narrow our view.

## Opening the Saved Packet Capture in Wireshark

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2001.png)

To open the Wireshark network traffic monitoring tool, I search for it in the Windows Start menu and click on the icon that appears.

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2002.png)

Once Wireshark’s interface loads, I select the “File” menu at the top of the window and choose the “Open” option to browse my local file directory for the packet capture file that I will be working with throughout this project.

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2003.png)

I navigate to the “Documents” folder and then enter the “Pcap Files” folder within it. There are four packet capture files stored in the directory, and I select the “2024–01–17.pcap” file to load into Wireshark’s interface.

## Filtering Network Traffic for IoCs

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2004.png)

Glancing at the bottom right-hand corner of Wireshark, we can see that 25,697 data packets have been captured. It would be extremely challenging to look for abnormal or malicious patterns within such a large packet capture, which is why filtering is necessary to narrow my scope.

Before applying any filters, I notice that a local IP address (10.1.17.101) has been involved in encrypted communication over port 443 with the external IP address 104.21.3.175. With this in mind, the first filter I want to apply is one that will show me what other servers my network has attempted to establish secure connections with.

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2005.png)

Applying the tls.handshake.type filter enables me to do just that. Now I see repeated connection attempts from the same local address to three other external IP addresses: 172.67.130.252, 162.159.135.233, and 103.28.36.229.

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2006.png)

To get a better idea of what is being hosted at the external addresses that my local device is trying to connect to, I apply the dns filter. I can see repeated DNS queries for broker.emqx.io, which has an external IP address of 54.146.113.169. This sort of persistent communication over such a short amount of time can be an indicator of beaconing associated with a command and control (C2) server. C2 servers are often used in botnet attacks, where an attacker infects several different hosts with malware that allows the C2 server to remotely control the infected host to conduct a more granular cyberattack (often a DDoS attack). The process in which the C2 server attempts to remotely activate the malware to take control of the infected host is called beaconing.

![Image](https://github.com/darylcbrooks/project-1/blob/project-33/Project%2033%20-%20Step%2007.png)

To test my theory, I apply the mqtt filter. MQ Telemetry Transport is a lightweight publish/subscribe network protocol designed for low-bandwidth, high-latency, or unreliable networks, commonly used for Internet of Things (IoT) and machine-to-machine (M2M) communication. It’s a rarely used protocol, therefore an ideal place to look if an attacker is trying to remain covert when communicating with my network. As suspected, I see the external address 54.146.113.169 showing constant communication attempts with 10.1.17.101’s MQTT port, which further supports my belief that this is beaconing behavior.

## Conclusion

In this scenario, I decided to narrow my focus to the MQTT protocol; however, applying filters for other rarely used ports can be just as effective. This is another example of why it is vital to close any unused ports and filter traffic for ports in use as a system hardening strategy. Attackers will often use open, seldom-used ports as a point of entry into a host. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
