### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/VJJ9ADO2Ne8/0.jpg)](https://youtu.be/VJJ9ADO2Ne8)

### [Project 25 - Hardening Our Attack Surface 🔒 ](https://youtu.be/VJJ9ADO2Ne8)

In this project, I’ll be performing a variety of system hardening techniques to reduce the vulnerability of a Linux‑based system’s attack surface.

## SSH Hardening

To start things off, I’m going to disable SSH root login and limit the number of allowed failed authentication attempts before a user is locked out of the system. This will mitigate privilege‑escalation vectors and brute‑force credential‑harvesting attempts against the SSH protocol.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2001.png)

The Linux‑based system that I’m hardening is located at IP address 192.168.1.100. I establish an SSH session with it using the following command (containing my account username) and provide my account password to proceed: “ssh ubuntu-user@192.168.1.100”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2002.png)

Once connected to the device, I execute the “apt-get update” command to ensure that the system has the latest security updates.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2003.png)

After all necessary updates have completed, I open the SSH configuration file to determine whether any changes are needed (“sudo nano /etc/ssh/sshd_config”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2004.png)

Inside the configuration file, I scroll down and see that “PermitRootLogin” has a boolean value of “yes”. As explained in previous projects, using a Linux root account is dangerous because irreversible kernel‑level damage can be done to the operating system.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2005.png)

I change PermitRootLogin’s value to “no” to restrict SSH root login access to the system. Scrolling down further, I see that the “MaxAuthTries” setting is commented out and set to 6. This means the system is not currently enforcing it (resulting in no limit on authentication failures), and if it were enabled, it would allow an overly generous six failed attempts before locking out a user. To strengthen the security of SSH, I uncomment MaxAuthTries and set a stricter limit of three attempts (“MaxAuthTries 3”). To save the changes, I press Ctrl+X, hit “Y”, and then press Enter.

## Setting Rules in Iptables

Next, I set the default iptables policies to DROP for INPUT and FORWARD, and ACCEPT for OUTPUT, ensuring anything not explicitly allowed is denied.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2006.png)

I then apply the following rules:

* sudo iptables -P FORWARD DROP — to drop forwarded packets
* sudo iptables -P OUTPUT ACCEPT — to allow outgoing packets
* sudo iptables -A INPUT -i lo -j ACCEPT — to allow traffic into the loopback interface
* sudo iptables -A OUTPUT -o lo -j ACCEPT — to allow traffic from out of the loopback interface

These set of rules are necessary for the server to be able to communicate with itself.

Next:

* sudo iptables -A INPUT -p tcp — dport 22 -j ACCEPT — to allow ssh connections to manage the server remotely
* sudo iptables -A INPUT -p tcp — dport 80 -j ACCEPT
* sudo iptables -A INPUT -p tcp — dport 443 -j ACCEPT

The last two rules allow HTTP and HTTPS traffic for a hosted website.

Lastly, to save the iptables configuration so the newly added rules persist after a reboot, I execute: “sudo iptables-save | sudo tee /etc/iptables/rules.v4 > /dev/null”.

## Configuring Fail2Ban Software

Fail2Ban is an intrusion‑prevention framework that protects servers from brute‑force attacks.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2007.png)

First, I copy the current configuration file to create one I can customize (“sudo cp /etc/fail2ban/jail.{conf,local}”). Next, I open the new file with nano (“sudo nano /etc/fail2ban/jail.local”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2008.png)

Inside the configuration file, I scroll down and uncomment “#ignoreip = 127.0.0.1/8 ::1” and replace it with:
ignoreip = 127.0.0.1/8 192.168.1.100 192.168.1.1/24 .

This instructs Fail2Ban to ignore this system’s IP address and any IP on the entire subnet (implicitly denying all others).

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2009.png)

Scrolling to the “sshd” section, I add the following settings to prevent SSH brute‑force attacks by banning IPs after three failed login attempts within five minutes:

enabled = true
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
findtime = 300
bantime = 28800
ignoreip = 127.0.0.1

To save my changes, I press Ctrl+X, hit “Y”, and then press Enter.

## Hardening the TCP/IP Stack

The next set of actions will secure the network layer against common attacks such as SYN floods, IP spoofing, and other vulnerabilities.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2010.png)

I open the system control configuration file with nano (“sudo nano /etc/sysctl.conf”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2011.png)

Upon reviewing the file, I notice several important settings are commented out.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2012.png)

The first two lines I uncomment are:
“net.ipv4.conf.all.rp_filter = 1” — enables IP‑spoofing protection
“net.ipv4.tcp_syncookies = 1” — enables SYN cookies

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2013.png)

Next, I set the system to ignore ICMP redirect requests by uncommenting “net.ipv4.conf.all.accept_redirects = 0”. I then disable IP source routing by uncommenting “net.ipv4.conf.all.accept_source_route = 0”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2014.png)

Lastly, I enable martian logging by uncommenting “net.ipv4.conf.all.log_martians = 1”. Martians are packets whose source or destination address is invalid, unrouteable, or reserved for special use. I save the file using Ctrl+X, “Y”, and Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2015.png)

To apply the changes made to the system control configuration file, I execute the following command: “sudo sysctl -p”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-25/Project%2025%20-%20Step%2016.png)

To verify persistence, I check the status of the “net.ipv4.tcp_syncookies” setting by running: “sysctl net.ipv4.tcp_syncookies”.
The system returns the uncommented setting and its value.

## Conclusion

This was a practical demonstration of Defense‑In‑Depth and how multiple layers of technical security controls can be implemented to reduce vulnerability to various cyberattacks. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
