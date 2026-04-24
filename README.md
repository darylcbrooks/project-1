### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/PYcemhip_Kc/0.jpg)](https://youtu.be/PYcemhip_Kc)

### [Project 31 - Configuring Suricata IDS/IPS for Cyber Defense ](https://youtu.be/PYcemhip_Kc)

In this project, I will be going through the steps of configuring the Suricata IDS/IPS for network monitoring on a Linux-based system. Suricata is an open-source network intrusion detection system (IDS) and intrusion prevention system (IPS) that provides high-performance network traffic analysis and security monitoring.

## Installing Suricata

Before installing the actual Suricata application on my device, there are a few things in the Linux operating system that I must verify and potentially adjust in preparation.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2001.png)

First, I must ensure that I have the necessary package for managing software repositories installed on my device. The following command allows me to accomplish this: "sudo apt-get install -y software-properties-common". Any required packages that were missing or out of date have now been installed.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2002.png)

Next, I run the following command to add the Suricata stable repository to my system: "sudo add-apt-repository ppa:oisf/suricata-stable".

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2003.png)

After a few seconds, all the necessary files are downloaded and installed, and the repository is successfully added.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2004.png)

Now, I'll need to ensure that all repositories installed on my Linux distribution are updated. I execute the "sudo apt-get update -y" command to do so. After its completion, all installed repositories should be up to date.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2005.png)

After verifying and updating all necessary software packages and repositories, I execute the "sudo apt-get install suricata -y" command to install Suricata.

## Adjusting the Network Range to Be Monitored

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2006.png)

Now that Suricata has been successfully installed on my device, I need to edit its primary configuration file to view the default IP range set for network monitoring. I use the nano text editor to open the "suricata.yaml" configuration file for editing (sudo nano /etc/suricata/suricata.yaml).

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2007.png)

Inside the file, I can see that Suricata uses the range of IP addresses defined in HOME_NET to filter out traffic that originates from my internal network and focus its analysis on external traffic that may pose a threat. I will need to change this to reflect my subnet range so Suricata can begin monitoring my internal network. To do so, I'll need to find my CIDR address.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2008.png)

I open a new command terminal and use the "ip a" command to find the subnet range required for monitoring my network. I can see that my device is running on the "ens32" network interface and that my CIDR address is "192.168.1.100/24". This is the subnet range that I will need to replace the current range specified in the Suricata configuration file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2009.png)

Back in the configuration file, I replace the IP range in HOME_NET with "192.168.1.100/24".

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2010.png)

Now that I know what network interface my device is running on (ens32), I need to verify that the correct one is set in Suricata's configuration. I scroll down several lines in the file and find that "-interface" is set to "eth0", which is not my network interface.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2011.png)

I simply replace "eth0" with "ens32" to reflect the actual network interface that my device is using. To save all the changes made, I press Ctrl+X, hit Y, and then press Enter.

## Enabling Persistence in Suricata's Configuration

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2012.png)

Next, I'll need to update Suricata's rule sets. The "sudo suricata-update" command allows me to do so.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2013.png)

To test Suricata's new configuration settings, I run the following command: "sudo suricata -T -c /etc/suricata/suricata.yaml". The output confirms that everything was successfully loaded.

![Image](https://github.com/darylcbrooks/project-1/blob/project-31/Project%2031%20-%20Step%2014.png)

Finally, I restart Suricata to ensure that the configuration changes take effect ("sudo systemctl restart suricata.service"). Then I start up Suricata again and ensure continuous monitoring functionality will begin upon system boot ("sudo systemctl enable suricata.service").

## Conclusion

Intrusion detection and prevention systems are critical security controls required for advanced network threat monitoring; however, misconfigurations can render them useless. Verifying and testing proper configuration prior to deployment is vital to ensuring that an IDS/IPS is an effective contribution to an enterprise's defense-in-depth strategy. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
