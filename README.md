### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/Bdy5WHDxexc/0.jpg)](https://youtu.be/Bdy5WHDxexc)

### [Project 34 - Configuring Zeek for Network Security Analysis](https://youtu.be/Bdy5WHDxexc)

Zeek is an open-source network security monitoring tool that analyzes network traffic in real time, capturing logs and data, but it is not an active defense tool. In this project, I’ll be walking through the necessary steps to configure Zeek to begin running on a network.

## Specifying Zeek’s Network Interface

The first thing I need to do is check which network interface Zeek is currently set to. It must match my own in order for Zeek to properly monitor the traffic on my device.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2001.png)

I start by changing to Zeek’s home directory (cd /opt/zeek) and then moving to the “etc” folder within it (cd /opt/zeek/etc). The configuration file that I need to check and potentially modify is located here (node.cfg). I use the cat command to open it (cat node.cfg).

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2002.png)

Inside the configuration file, I can see that the current network interface is set to eth0. I need to verify what network interface my device is running on to ensure it aligns with Zeek’s configuration.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2003.png)

I execute the ifconfig command (since I’m using a Linux OS) to view my installed network interfaces and determine which one I’m using. The output indicates that my device is actually running on the ens32 interface, so I’ll need to modify Zeek’s configuration file to reflect this.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2004.png)

I use the nano text editor to open the node.cfg file for editing and provide my user credentials to proceed (sudo nano node.cfg).

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2006.png)

I change the network interface from eth0 to ens32. To save the changes, I press Ctrl+X, hit Y, and then press Enter.

## Modifying Bash Shell For Zeek Ease Of Use

Next, I want to update the configuration of the bash terminal on my device so I can run Zeek commands from any directory without having to specify the fully qualified path.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2007.png)

I use the nano text editor to open the bashrc file (nano ~/.bashrc) and add Zeek’s home directory path to it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2008.png)

At the very bottom of the file, I add the following line:
export PATH=/opt/zeek/bin:$PATH

To save the changes, I press Ctrl+X, hit Y, and then press Enter. Back on the command line, I execute the source ~/.bashrc command, which reloads the bashrc file to apply the update.

## Deploying Zeek

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2009.png)

I switch to the directory containing the file I need to execute Zeek (cd /opt/zeek/bin). zeekctl is a command-line tool designed to manage and control the Zeek network analysis framework.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2010.png)

First, I execute zeekctl with the check command to validate the Zeek configuration and ensure all components and dependencies are properly set up and ready for deployment (sudo ./zeekctl check). The output confirms that everything is good to go.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2011.png)

Next, I execute zeekctl with the deploy command to start Zeek.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2012.png)

To confirm that Zeek has been successfully deployed, I execute zeekctl status. The output shows that the status is “running.”

## Generating and Viewing Zeek Logs

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2013.png)

To view the contents of Zeek’s log directory, I use the ls command with the directory name (sudo ls /opt/zeek/logs). The results show a subdirectory named current.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2014.png)

I run the ls command again, this time including the current folder in the path (sudo ls /opt/zeek/logs/current). Several different log files appear; however, I want to create a bit of network traffic to demonstrate Zeek’s capturing capabilities and view the logs that are subsequently generated.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2015.png)

I run the nslookup command against google.com to generate DNS (Domain Name System) query logs, which will be stored in an auto‑generated log file named conn.log (nslookup google.com).

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2016.png)

I execute sudo cat /opt/zeek/logs/current/conn.log to view the output. conn.log is a key file used by Zeek to record details of all observed network connections. It captures essential information such as timing, participants, and methods of each connection by checking timestamps and IP addresses to track activity over time.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2017.png)

Next, I use the following command to view another log file generated from my nslookup, dns.log:
sudo cat /opt/zeek/logs/current/dns.log

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2018.png)

dns.log is a critical, high‑detail log file providing a comprehensive record of all DNS activity observed on the network. It goes beyond standard DNS logs by capturing queries, returned IP addresses, response codes, and query types.

![Image](https://github.com/darylcbrooks/project-1/blob/project-34/Project%2034%20-%20Step%2019.png)

Lastly, I run sudo cat /opt/zeek/logs/current/weird.log to view the contents of the weird.log file that was also generated by my recent nslookup. weird.log contains unusual, unexpected, or exceptional network activity that does not strictly conform to protocol standards or analyzer expectations. It highlights potential malformed packets, network malfunctions, misconfigurations, or evasive behavior by an attacker—though many entries are benign or simply high‑volume noise.

## Conclusion

This was a simple demonstration of how Zeek can be configured on a Linux device for network security monitoring and how to view common network traffic logs. In my next project, I’ll be diving deeper into Zeek log analysis to draw crucial conclusions about a system’s security posture. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉  

