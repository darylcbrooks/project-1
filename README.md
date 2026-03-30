### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/VCsI4h3JtPc/0.jpg)](https://youtu.be/VCsI4h3JtPc)

### [ Project 19 - Brute forcing into a router with Hydra](https://youtu.be/VCsI4h3JtPc)

Putting on my “red team jersey again”: the aim of this project is to gain entry to a fictitious organization’s router by brute forcing its login credentials and modifying its configuration settings to maintain persistence on their network. The tool that I utilized to accomplish this is Hydra. Hydra is a pre-installed tool on Kali Linux and is widely recognized as the fastest and most flexible network login cracker in the cybersecurity industry. It is designed to perform rapid dictionary and brute-force attacks against login services. Without further delay, let’s get started!

## Breaking Into a Router w/ Hydra

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2001.png)

To ensure that I have the proper level of privileges to run the necessary commands for this brute force attack, I run the “sudo su” command to switch to the Kali’s root user account. This step is for demonstration purposes only, but this is highly discouraged due to the unintentional damage that can be done to the operating system and underlying technology that it is running on.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2002.png)

Next, I run the following command to run an ARP scan that will map devices that are present on the target network: “arp-scan — localnet”. ARP scans provide a list of physical or MAC addresses of the devices running on a target network and the IP address associated with each of them. The result of the ARP scan returns with a list of three devices, and I’ll begin my device enumeration with the last IP address on the list, 172.20.1.102.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2003.png)

I conduct a Nmap service scan on 172.20.1.102 to find out what ports are open on the device, along with what version of services are being ran on the open ports (“nmap -sV 172.20.1.102”). Based the results of the scan, I can see that port 22 is the only port that’s open, is running OpenSSH version 8.2p1, and the device appears to be running on the Ubuntu Linux distribution as its operating system.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2004.png)

Before preparing to brute force the SSH credentials on the target host, I run the “cd ..” command twice to enter the root account’s root directory. This is where I will store the custom wordlists that I’m creating and will be using as part of the brute force attack.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2005.png)

First, I create a wordlist for Hydra to use in order to crack the passwords for any discovered accounts. I use the nano text editor to accomplish this and name the file “passwords.txt” (“nano passwords.txt”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2006.png)

Once in the text editor, I list out seven different strings of text that Hydra will read and attempt to use as passwords in combination with any found user accounts to authenticate over the SSH protocol into the target host. After I’ve entered in the list, I use the Ctrl + X key combination to exit the text editor and press Y to save my work.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2007.png)

Now that I’ve created a wordlist for Hydra to use to crack account passwords, I move on to create the wordlist it will need to find account names to use in combination with the cracked passwords in order to successfully authenticate to the target host. Again, I use the nano text editor to create the wordlist named “username.txt” (“nano username.txt”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2008.png)

Similar as before, I enter in a list of five strings of text that Hydra will read and attempt to use as username accounts in combination with any cracked passwords to authenticate over the SSH protocol. After I’ve entered in the list, I use the Ctrl + X key combination to exit the text editor and press Y to save my work.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2009.png)

Having created both a username and password wordlist, it's time to run the brute force attack with Hydra! I run the following command, providing Hydra with the usernames.txt file to find any matching account name syntax, the passwords.txt file to crack any matching account passwords, and the target protocol & IP address: “hydra -L usernames.txt -P passwords.txt ssh://172.20.1.102 -t 4” (the “- t 4” portion of command allows four parallel tasks to run). Within a matter of seconds, Hydra returns with the successful username and password combination of “admin, admin”. There are a few reasons why this brute force attack was able to succeed and they revolve around the password policy in use. This could have been prevented by implementing strict password complexity requirements and having a strict account lock out policy with a low minimum number of failed login attempts.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2010.png)

Now that I apparently have a set of SSH login credentials, I use the following command in an attempt to authenticate to the target host: “ssh admin@172.20.1.102”. I’m able to successfully login and given the naming convention of the user account, it appears that I’ve gained access to a router! My next step is to enumerate the configuration settings of this router to enable my ability to pivot to other devices and/or maintain persistence on the victim’s network.

## Enumerating Router Settings

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2011.png)

In order to view the configuration settings of the router, I must first switch to the configuration terminal. I enter the “configure terminal” command, however, it appears to have failed. This is because I must be in the “Privileged EXEC” mode to be able to execute this command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2012.png)

Entering Privileged EXEC mode is fairly simple. Just typing in the “enable” command switches me over to the necessary mode of operation. One way to be sure of which mode is currently active is to take note of the symbol next to the username. The “#” character indicates that Privileged EXEC mode is use, whereas the “>” character indicates that normal mode is being used.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2013.png)

Now that I’m in the correct user mode, I enter the “configure terminal” command again and now have the ability modify the configuration settings of the router. However, I’m not ready to do so as of yet and just wanted to demonstrate this step as an example. I execute the “exit” command to no longer be in “config” mode.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2014.png)

To enumerate the different network interfaces on the router, I use the “show interfaces” command while in Privilege EXEC mode. The amount of interfaces present depends on the number of interface cards installed on the router.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2015.png)

The details of the interfaces can be reduced to just the most important information in a summary format. This is accomplished by using the “show ip interface brief” command. This bit of information will be important once I begin port forwarding.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2016.png)

The Cisco Discovery Protocol (cdp) operates at layer 2 of the OSI model (data link) and displays the information of the devices that the router is connected to. In order to view this information, the “show cdp neighbors” command must be executed.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2018.png)
![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2019.png)

The “show running config” command shows the current router configuration settings. In the image above, we can see information such the address of the DNS server, the network domain, the login credentials for the router and also the nature of some of the devices assigned to other IP addresses on the network. We can see a printer (172.19.0.5), a web server (172.19.0.6), four workstations (172.19.0.2, 172.19.0.3, 172.19.0.4, 172.19.0.5) and another name server (2.2.2.2). It also appears that the printer is associated with workstation # 4.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2020.png)

If I want to see the MAC addresses of the devices associated with these IP addresses, executing the “show ip dhcp binding” command allows me to do so.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2021.png)

To view this same information for just the router itself, I would use the “show arp” command. This also displays the name of the internal interface that the router is running on.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2022.png)

If I want to view the same information on the devices that I found while running the DCHP biding command but in a much cleaner format, I will simply run the “show hosts” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2023.png)

Now that I’ve enumerated some configuration details about the router and some of its neighbors, I’m going to jump back into config mode by running the “configure terminal” command again.

## Manipulating Network Traffic

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2024.png)

I use the following command to access the firewall and create a rule to allow the router to reach any destination: “access-list 1 permit 172.19.0.0.0.255.255 any”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2025.png)

Next, I enable NAT overload and bind it to the selected outside interface. This is going to mask my private IP address while conserving public address space (ip nat inside source list 1 interface).

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2026.png)

In the following set of steps, I configure the internal interface for port forwarding. Port forwarding maps external IP addresses & ports to internal IP addresses & ports, allowing access to internal services from the outside internet. I run the “interface gigabitEthernet 0/0” command to configure the internal interface for port forwarding. Now in “config-if” mode, I run the following command to translate the source IP address of packets that travel from inside the network to outside: “ip nat inside”. Having completed this portion of the port forwarding task, I execute the “exit” command to leave “config-if” mode.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2027.png)

Now it's time to do the reverse and configure the external interface for port forwarding. The “interface gigabitEthernet 0/1” command puts me back into “config-if” mode which allows me to modify the configuration of the external interface. Similar to the internal interface’s configuration change, the “ip nat outside” command is used to translate the source IP address of packets that travel from outside the network to inside. I execute the “exit” command to leave “config-if” mode.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2028.png)

Having configured how the router translates ingress and egress activity, I must now enable port forwarding on both the public facing web server, 172.19.0.6 and on the internal router, 172.20.1.102. The following command allows me to do so and ensures that port 80 (default HTTP port) is open on both devices: “ip nat inside source static tcp 172.19.0.6 80 172.20.1.102 80”.

## Demonstrating The Results

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2029.png)

First, I must verify that the router is publicly accessible from port 80. I run another Nmap scan and the results confirm that port 80 is now open!

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2030.png)

Next, I test the public availability of the router by opening a browser and navigating to its IP address (172.20.1.102). I’m now greeted with a web page that simply reads “Website Is Under Construction”. While there’s nothing of much value there to enumerate, it verifies that I can publicly access the router from an outside device now!

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2031.png)

Back in the command terminal, I authenticate to the router through the SSH protocol again but this time I have it to listen on locally accessible port 8888 (which is not currently being used) which forwards the traffic to port 80 (“ssh -L 8888:172.20.1.102:80 admin@172.20.1.102). Meaning that, the traffic for unused ports will show me whatever is being hosted on port 80 since the outside traffic is being forwarded there now.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2032.png)

To test that this works, I enter “localhost:8888” in the web browser while I’m authenticated through SSH in the command terminal. I receive the same results as before when just connecting directly to port 80.

![Image](https://github.com/darylcbrooks/project-1/blob/project-19/Project%2019%20-%20Step%2033.png)

I exit SSH and reauthenticate but this time to test if the remote port forwarding is working correctly. This will allow a remote user to access resources on the local machine. I run the following command to test this: “ssh -R 8888:localhost:8080 admin@172.20.1.102”. Users with access to the router will be able to view all the content of port 8080 of the localhost in the 8888 router port.


Since this project was done in a lab environment, this aspect was a bit difficult to test. However, given the positive results that I received when testing the port forwarding from the other connections, I’m positive that this remote access should work as well.

## Conclusion

In this project, I showed how a something as simple as a weak password policy can snowball into an attacker pivoting to an internal router and creating a backdoor to maintain persistence via port forwarding. Sometimes the most critical areas of Defense-In-Depth are the simplest and easiest to secure. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
