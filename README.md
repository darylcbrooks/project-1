### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/sxZeefLZaco/0.jpg)](https://youtu.be/sxZeefLZaco)

### [Project 12 - Exploiting Vulnerabilities w/ Metasploit (Ethical Hacking!!!) ](https://youtu.be/sxZeefLZaco)

Welcome to my first of many Metasploit Framework (MSF) projects! Though I won’t be diving very deep into the many layers and functionalities of the MSF, I will be demonstrating how to execute a simple exploitation against a found vulnerability. What exactly is the Metasploit Framework? It’s a powerful and versatile tool utilized by ethical hackers and penetration testers to identify, exploit, and mitigate vulnerabilities in systems. Let’s see how it works:

### Active Reconnaissance w/ Nmap

Before launching the MSF, I must first conduct what is known as active recon against a target host. As indicated, there are two different reconnaissance types: passive and active. Both involve gathering information about a potential target but take different approaches. Passive recon consists of techniques that are more subtle and less detectable, such as using a search engine to gather information about the company hosting the targeted network. This can help with deducing details such as email extensions or potential passwords internal users may be inclined to use.

On the other hand, active reconnaissance involves more direct — but arguably more effective — ways to gather internal information on a target system or network, such as port scanning. The information gathered here can provide technical insight such as open ports, protocols in use, versions of services running, and any known vulnerabilities associated with those details. In this project, I will be using the Nmap tool to conduct active recon via a port scan against an already discovered host.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2001.png)

My target host has an IP address of 192.168.1.101, and I will be running a service version scan against it (“nmap -sV 192.168.1.101”). Adding the “-sV” option to my Nmap scan returns results containing the versions of the services running on the discovered open ports. We can see in the image above that ports 21 (FTP), 22 (SSH), 25 (SMTP), 3306 (MySQL), and 3389 (RDP) were found to be open. The services being run are vsftpd 2.3.4 (port 21), OpenSSH 9.4p1 Debian 1 (port 22), Postfix smtpd (port 25), and xrdp (port 3389). Out of these four discovered ports running services, I’m going to narrow my focus to port 21 (FTP). As discussed in previous projects, FTP is an insecure protocol and has many vulnerabilities. The first that I want to test for is the allowance of anonymous logins.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2002.png)

Anonymous FTP logins allow a user to simply use “anonymous” as a username and log in to the FTP server without providing a password. To test this, enter the following command to initiate an FTP session with the target host: “ftp 192.168.1.101”. When asked for a username, I enter “anonymous,” leave the password line blank, and hit Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2003.png)

I receive a 203 return code, which notifies me of a successful login. Our first vulnerability has been found and verified before even using the Metasploit Framework!

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2004.png)

While I’m in here, let’s have a look around. I use the “ls -al” command to list out all files (including hidden ones) present in the current working directory. There doesn’t appear to be anything here to see, so I terminate my FTP session with the target host by typing the “bye” command.

### Exploiting Service Version Vulnerabilities w/ Metasploit

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2005.png)

Now that I’ve conducted some active recon, enumerated some open ports and services, and even found a way into our target host, let’s get started having a bit of fun with the Metasploit Framework. Usually, to initiate the MSF, the following command must be executed to start the PostgreSQL database that Metasploit heavily relies on: “service postgresql start.” After entering this command, the “msfconsole” command must then be executed to boot up the msfconsole, which allows me to directly interact with the framework. However, due to how my virtual environment is configured, in this instance, I just have to use the “msfconsole” option to move forward. As a side note, these two commands can actually be used together as a single command, reducing the number of steps needed to properly initiate MSF: “service postgresql start && msfconsole.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2006.png)

After a few seconds, the msfconsole fully boots up. Now that I’m in Metasploit, the first thing I want to do is search for the service and version that I’m targeting. In this instance, it’s “vsftpd 2.3.4,” which is running on port 21 (“search vsftpd 2.3.4”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2007.png)

Metasploit searches its database and returns all related modules to my query. These module types can range from auxiliary scanners, exploit modules, to post‑exploitation modules. In my case, Metasploit only found one module related to vsftpd 2.3.4; however, it is an exploit module. By reading the module description, we can see that it’s designed to automate the exploitation of a known vulnerability in vsftpd 2.3.4 that creates a backdoor to the system it’s running on. There are two ways that I can tell MSF that I want to use this module: I can either type “use” along with the module name (“exploit/unix/ftp/vsftpd_234_backdoor”) or type “use” along with the module ID, which in this case is zero (“use 0”). I use the latter method and hit Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2008.png)

To begin viewing and configuring the settings of this exploitation module, I execute the “show options” command. From the list of variables that I can set, the only one that requires modification is the “RHOSTS” (Remote Host) option.* This is where I enter either the hostname or IP address of the target system.

*Note: RPORT is set to 21 by default since this is an FTP‑related module.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2009.png)

In this case, it’s going to be 192.168.1.101 (“set RHOSTS 192.168.1.101”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2010.png)

When I execute the “show options” command again, we can see that the target IP address has now been set as the RHOST.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2011.png)

With everything properly set up in the module’s options, I can execute it against the target host by using the “exploit” command. Metasploit returns a series of messages, walking through the steps it’s taking on the backend to make a connection with the host. Based on these messages, we can see that a backdoor service has been spawned, which overrides the initial request for a password, and based on the attributes of the UID in use, it appears that I’ve been granted root access. Take notice that after the “Command shell session 1 opened” message, the screen appears blank. This is due to the module opening a non‑interactive shell on the target host. I’m in, but I’m not using any sort of known shells such as Bash, Bourne, or Korn. There is a way to switch to one of these, but this is outside the scope of this project and will be covered in a future one.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2012.png)

To verify that I have root access to the target host, I execute the “whoami” command, which returns my username as “root.” This means that I’ve established a connection with our target host with elevated privileges, allowing me to execute pretty much any command that I want on this system!

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2013.png)

If I want to view all the subdirectories from my current location in the system, I use the “dir” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2014.png)

I can back out of this session with the target host while still maintaining access by either using the Ctrl + Z key combination or executing the “background” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2015.png)

Back in the msfconsole, I can view all of my active sessions by executing the “sessions” command. We can see my current shell session with the target host.

![Image](https://github.com/darylcbrooks/project-1/blob/project-12/Project%2012%20-%20Step%2016.png)

Whenever I want to return to an active session, I simply execute “sessions” and specify the session ID with the “-i” option and the session number after it. In this instance, I enter “sessions -i 1” since I only have one active session available, and 1 is the session ID assigned to it. Once I’m back in my session with the target host, I execute the “whoami” command again to verify everything is still working properly.

### Conclusion

This was a very basic walkthrough of how to enumerate system vulnerabilities with Nmap and exploit those vulnerabilities with the Metasploit Framework. In future projects, I will dive deeper into how we can use both of these tools to enumerate even more details and find additional ways into insecure systems. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
