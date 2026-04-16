### Click Header Image or Project Title to Watch The Video

[![Watch the video](https://img.youtube.com/vi/sslRwRCRm0w/0.jpg)](https://www.youtube.com/watch?v=sslRwRCRm0w)

### [Project 03 - Active Recon with NMAP](https://youtu.be/sslRwRCRm0w)

In project 3, I dive into ways ethical and malicious hackers can utilize the Nmap tool to enumerate details on target devices and networks. So, what exactly is Nmap? It's a free open-source network utility used for network discovery and security auditing. In terms of the MITRE AT&TACK framework, the Nmap tool is often used in the “Reconnaissance” stage, taking an active approach against target machines. The way Nmap works is that it sends an ICMP (ping) packet to a specified host to identify whether if its active and then sends “SYN” packets to initiate a connection with various ports to simply see if they’re open but doesn’t attempt to complete the associated “3-way handshake” (I will discuss this further). These default mechanisms can be altered in various scan types to gain access to whatever information is being sought. Without further ado, let's take a look at a few variations of Nmap scans and discuss what they’re commonly used for:

## *Nmap TCP Scan*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2001.png)

Typing the command “nmap -sT <ip address/hostname>” performs a Nmap TCP scan. This will scan the target host for any open TCP ports. The TCP protocol establishes what is known as the “three-way handshake connection” with the initiating device sending a “SYN” packet, then the responding device sending back a “SYN-ACK” packet, and finally the first device in the sequence sending a “ACK” packet establishing the connection between the two. In the above example, the Nmap TCP Scan has been ran against a host named “skillsetlocal.com” (which is just my local loopback address). It returns notifying me that three open TCP ports has been found open: SSH (port 22), HTTP (port 80), and RDP (3389).

## *Nmap Ping Sweeps*

A ping sweep is a method that can be used to find other active hosts within the local network. While ping sweeps aren’t exclusive to the Nmap tool, it can certainly be done using it. I will show how we can do so with and without Nmap.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2002.png)

In order to conduct a ping sweep with Nmap, we must first know what our IP address is. Since I’m using the Linux operating system in this lab, I will be using the “ifconfig” command to display it (opposed to the “ipconfig” command used in Windows environments). In the above image, my device is running on interface “eth0” with an IP address of 192.168.100.101.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2003.png)

Now that I know my IP address, this gives me insight to what my CIDR address is. CIDR’s use a notation with a slash and a number to specify a black of IP addresses, allowing for smaller, more manageable subnets. So since I know that my IP address is 192.168.100.101, then my CIDR is going to be 192.168.100.0/24. To conduct the ping sweep, I’ll use the “nmap -sn” command with my CIDR, executing “nmap -sn 192.168.100.0/24”. This returns two found hosts: Metasploitable (192.168.100.100) and myself, Student (192.168.100.101).

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2004.png)

Other way we can view hosts associated with the network (not necessary active) is by displaying the contents of the hosts file located in the “/etc/hosts” directory. In order to view this, use the “cat” command in combination with the “/etc/hosts” directory. In the above example, we can see that the output of that file is the same as the ping sweep that I conducted with Nmap. There are other methods to conduct ping sweeps beyond these examples, however I don’t want to digress too far from the primary focus of this lab.

## *Targeting Multiple Hosts*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2005.png)

There are various ways that Nmap can be used to target multiple hosts within the same network in a single scan. If we want to specify the IP addresses to target on the command line, type “nmap”, “parameters for the scan type”, “full ip address of first target”, “comma”, and “the last octet values of the other targets”. The last octet value services as somewhat of an ID of where the device lives on the network since its unique to it. In the example above, I ran a Nmap TCP scan against both IP addresses found in the previous ping sweep (192.168.100.100 and 192.168.100.101). This is expressed in the command, “nmap -sT 192.100.100.100,101”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2006.png)

Another way that we can scan multiple hosts at a time within a local network using Nmap, is by including an IP range within the command. This is done by typing “nmap”, “parameters for the scan type”, “first three octets of the IP address”, “last octet of the first address”, “dash”, and “last octet of the last address”. Observing the above example, I performed a TCP scan of all devices on 192.168.100.1 through 192.168.100.100. Yes, I requested Nmap to scan all ports for 100 addresses on my local network. Fortunately, my machine was the only device active within that range or else I would have been swimming in a sea of output 😄. The command to accomplish this was “nmap -sT 192.168.100.1–100”.

## *Targeting Specific Ports*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2007.png)

Nmap scans’ focus can be narrowed further to just looking to see if specific ports are open. This done by adding the -p (port) parameter to the command line and adding the desired port number(s) after it. Multiple specified ports can be scanned as well. This works exactly the same as specifying multiple individual IP address and IP address ranges. Just add a comma after the first specified port & type the next port number (ex. -p 80,22) OR add a dash after the first specified port & type the last port to be scanned in the case of a port range scan (ex. — p 22–80). In my example, I ran a TCP scan against the skillsetlocal.com host to see if port 6379 is open. This is expressed as “nmap -sT skillsetlocal.com -p 6379” on the command line. The output that Nmap returns back with informs me that port 6379 is closed and belongs to the redis protocol. As a side note, the redis protocol is used for client/server communication.

## *Nmap UDP Scan*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2008.png)

To find open UDP ports on target hosts, a UDP scan can be utilized by Nmap. Similar to the TCP scan, a “-sU” parameter needs to be added to the command line followed by the IP address or hostname of the target. Unlike TCP, UDP is a connectionless protocol used for things such as streaming. I conducted an UDP scan against the skillsetlocal.com host using the “sudo nmap -sU skillsetlocal.com” command. Sudo provides with the necessary user privileges to run this command without having to use the root user account. Due to this, I have to provide Kali my account password to run the command. The output received notifies me that port 123 is the only UDP port open on this host. Port 123 belongs to the NTP (network time protocol) which is responsible for synchronizing device times across a network.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2009.png)

A TCP and UDP scan can both run simultaneously by simply using the “-sU” & “-sT” together on the command line using sudo and specifying the host. When running the “sudo nmap -sU -sT skillsetlocal.com” command, I now get a full list of all the previous ports discovered when conducting the other scans.

## *Storing Nmap Results*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2010.png)

There are several different ways that Nmap results can be stored and this project covers just a few. In the above image, I conduct another ping sweep but this time I add a “-oG pingscan.out” parameter. This tells Nmap to store the results of the ping sweep in a greppable format (-oG) to a document named “pingscan.out”. Greppable format is one of three different formats that Linux can be saved in and is considered the most “human readable”. The other two are Normal format (-oN) which is just an exact copy of how the output would regularly appear when displayed on the screen and XML format (-oX), which is a machine readable markup language.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2011.png)

To view the contents of the new “pingscan.out” file, simply use the “cat” command with the file name. When I run “cat pingscan.out”, Kali returns both previously discovered IP addresses found in the ping sweep while also providing that they’re status of being “Up”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2012.png)

Adding the “| grep Up” parameter adds a word search feature to the command. Now anywhere within the document that contains the word “Up” will be highlighted in red by Kali. This feature is highly useful in longer documents where we just want to find certain key terms when conducting analysis.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2013.png)

If we want to save the version of the ping sweep output file that highlights the “Up” keyword to different document, we can add the “> pingscan.out1” parameter to the end of the previous command. This tells Kali to output everything to the left of “>” to a new file called “pingscan.out1”. In addition to running this command, in the above image, I used the “cat” command to display the output of the new file. Due it the brief nature of its contents, the output looks identical to the original “pingscan.out” file. However, in cases where the original output was much longer, only the portions containing the specified keyword would be included in the “pingscan.out1” document.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2014.png)

We can further isolate specific details and save them to another document. In my example, I decided that I wanted to just pull everywhere that contained IP addresses into a new document. I replace the “grep Up” parameter with “cut -f2 -d” “ > pingscan.out2” which tells Kali to take everything that’s in the 2nd field in each line and save it to a new document named “pingscan.out2”. When running the “cat” command with the new document name, we can see that only the two IP addresses are displayed. This is why greppable format is preferable in this instance. Because the output is organized into fields, I can specify which information to view or save from particular fields.

## *Performing Document Scans With Nmap*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2015.png)

Now that I’ve isolated the IP address from the original ping sweep and saved them to a document, I can use Nmap to scan the IP addresses listed in this document for port discovery. This is accomplished by using the “-iL” or “input from list” parameter followed by specifying the document name. The above image shows that I ran a TCP scan against the pingscan.out2 document and specify that Nmap checks if the RDP port is open (3389). Nmap finds that port 3389 is closed on 192.168.100.100 but is open on 192.168.100.101 (my IP address).

## *Nmap Operating System Scan*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2016.png)

Nmap can be used to enumerate details about a target host’s operating system. This includes OS version and kernel version. As a cyber analyst or ethical hacker, this can be vital information to know as there are version specific vulnerabilities that can be exploited. To run an OS scan against a host, simply add the “-O” parameter and specify the target’s IP address or hostname (of course using sudo if using any other user account other than root). In the above example, I ran the “sudo nmap -O skillsetlocal.com” command and can see that my machine is running Linux version 2.6.32.

## *Adjusting The Speed Of An Nmap Scan*

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2017.png)

Users can control how fast Nmap scans hosts for open ports. The faster Nmap runs, the easier it is for it to be detected. Also, running Nmap at its fastest speed can potentially crash the target system, so we must be careful in how we use these speed parameters. The “T” parameter is what enables us to control the speed of the scans. The speed parameters that we can use on the command line are as follows (from slowest to fastest):

* T0 (paranoid)
* T1 (sneaky)
* T2 (polite)
* T3 (default)
* T4 (Aggressive)
* T5 (Insane)

The lower the number the slower the scan, but the stealthy we are and the lower the risk of being detected. Vice versa with the higher numbered parameters and also the risk of a system crash. In the above example, I run an aggressive Nmap scan (-T4) against the skillsetlocal.com host as well as using the “-A” parameter. The -A parameter combines various scan types together to provide us with highly detailed information about the target.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2018.png)

Similar to the “-A” parameter, the “-v” parameter (short for verbose) provides Nmap users with a full report of on a scan containing details that maybe otherwise be omitted. An example of such can be seen in the above image.

![Image](https://github.com/darylcbrooks/project-1/blob/project-3/Project%203%20-%20Step%2019.png)

Last but certainly not least, is the “fast scan” which can be utilized by specifying the “-F” parameter on the command line. This type of Nmap scan speeds things up by scanning the 100 most popular ports.

## *Conclusion*

Which brings us to the end of this walkthrough for this project. Hopefully this gave everyone reading more insight as to how Nmap works and different commands that can be used to help make device enumeration a more efficient process. As always, I have a video walkthrough of this very same lab available for your view pleasure on Youtube (click the link above to watch). Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉

