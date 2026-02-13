### Click Header Image or Project Title to Watch The Video

[![Watch the video](https://img.youtube.com/vi/IUnIReIs2kY/maxresdefault.jpg)](https://youtu.be/IUnIReIs2kY)

### [Project 04 - Packet Analysis with Tshark: Telnet vs. SSH](https://youtu.be/IUnIReIs2kY)

In this project, I will demonstrate the importance of using secure variations of protocols instead of plaintext. In this instance, the output of two connection-based protocols will be compared: Telnet and SSH. To view the output of traffic sent over a network by both protocols, the Tshark packet analysis tool will be used.
Telnet is a text-based network protocol that allows a user on one device to log into another device that is part of the same network. On the other hand, SSH is a network protocol that creates a secure, encrypted connection between two devices over an unsecured network, such as the internet. Let's dive in (pun fully intended):

### Restarting Telnet and SSH

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2001.png)

First things first, I'm restarting both the Telnet and SSH protocols to ensure they are in listening mode, making them fully accessible. To do this, I need root privileges without the inherent risks that come with using Kali's root account. The sudo utility allows me to do just that as long as I provide it with my account password.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2002.png)

Next, I'm going to use the netstat -tuln command to list the running protocols and their state to verify that both Telnet and SSH are processing in a listening state. In the above image, both ports 22 (SSH) and 23 (Telnet) can be observed on the list with their state listed as "LISTEN."

### Capturing & Analyzing Telnet Traffic

Now that I know both SSH and Telnet are running, I can begin the process of capturing their network traffic. I will utilize two separate terminals for this phase: one for packet capturing and the other for viewing connections.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2003.png)

In the current window, I first need to give myself write permissions to copy and save the network traffic output into a text editor. The sudo chmod o+w . command enables me to do so.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2004.png)

The next command (sudo tshark -i lo -w telnet.pcap) starts the Tshark command-line packet capturing tool on the loopback network interface (lo), writing the Telnet traffic output to a file called telnet.pcap. Note: "pcap" is short for "packet capture" and is the primary format of files read by both Tshark and its GUI-based cousin, Wireshark.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2005.png)

After opening a new terminal window, I log in to Telnet using my account credentials (telnet localhost) to begin generating some traffic to port 23.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2006.png)

Next, I use the who command to verify that I have an established Telnet session. Once confirmed, I type exit to close the connection.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2007.png)

Back in the first terminal that's running Tshark, I use the key combination of Ctrl + C to stop Tshark's packet capture. Then I use a command that tells Tshark to look for anywhere in the telnet.pcap file that says "Password" and to display only 20 lines after it:
sudo tshark -r telnet.pcap -q -z follow,tcp,ascii,0 | grep -A 20 -i "Password"

From the image above, we can see a vertical string of alphanumeric characters displayed in response to the executed command. The letters and special characters seen between the 1s represent my cleartext password (Passw0rd!). This is an example of why using Telnet or any other unencrypted protocol is ill-advised. In reality, if a malicious actor were to have an entity's IP address and begin capturing traffic on an insecure port, they would have the same opportunity to capture user login credentials. This is why it's best practice to use a secure alternative such as SSH.

### Capturing & Analyzing SSH Traffic

Now let's see what SSH traffic looks like when captured:

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2008.png)

Similar to Telnet, I start Tshark again on the loopback network interface (lo), writing the SSH traffic output to a file called ssh.pcap.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2009.png)

Switching back to the second terminal, I authenticate to the same local machine using SSH.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2010.png)

To generate more traffic for Tshark to capture, I execute the pwd command, which lists my current working directory and also verifies that I have an established SSH connection. Finally, I type exit to terminate the connection.

![Image](https://github.com/darylcbrooks/project-1/blob/project-4/Project%204%20-%20Step%2011.png)

Back in the Tshark terminal, I use the key combination Ctrl + C to stop the packet capture and enter a command that allows me to view the contents of the ssh.pcap file, which contains the most recent packet capture:
sudo tshark -r ssh.pcap -q -z follow,tcp,ascii,0 | more

There are some noticeable differences with this command to read the pcap file, including the inability to specify to Tshark to look for the term "Password." Since the SSH protocol is encrypted, I have to pipe (|) and just use more instead. In the image above, the output is indeed in ciphertext and not human-readable.

### Conclusion

…And that brings me to the end of Project 4. The purpose of this project was to provide a clear example of why it's best practice to not only avoid using cleartext protocols but to disable them by default to harden a system's attack surface and also to display the advantage of using encrypted protocols as a secure alternative. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
