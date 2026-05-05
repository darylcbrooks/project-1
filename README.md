### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/jadjNIHYHo0/maxresdefault.jpg)](https://youtu.be/jadjNIHYHo0)

### [Project 37 - Understanding the Security Risks of Misconfigured Sudo in Linux ⚠️ ](https://youtu.be/jadjNIHYHo0)

In this project, I’ll be demonstrating some ways the Linux sudo utility can be exploited if it is not properly configured. Sudo is the tool that gives Linux users the ability to run commands with root privileges without actually using the root account. When not carefully set up, sudo can also allow users to do far more than intended. Below, I will provide a few examples of commands that should require an authentication layer of security before being allowed to run with sudo.

## Running the Base64 Command Using Sudo

Base64 is an obfuscation and encoding scheme that converts binary data into a text string using a set of 64 printable ASCII characters.

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2001.png)

I use the following command to read the shadow password file (/etc/shadow), encode it, and then immediately decode the file:
sudo base64 /etc/shadow | base64 -decode.

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2002.png)

If a user can run sudo along with the Base64 command, they can read any file on the server with a command such as this.

## Running the File Command Using Sudo

Using the file command with a file name outputs the type of file that it is.

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2003.png)

For example, using the file command with the byteshred.c file identifies the file as source code (file ~/Desktop/git/byteshred.c).

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2004.png)

Now, let’s see what happens when I’m allowed to use sudo with the shadow password file (sudo file -f /etc/shadow).

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2005.png)

I receive error messages that allow me to read the contents of this directory, which include password hashes. A malicious user can take these password hashes and use cracking tools such as John the Ripper or Hashcat to decrypt them into cleartext. When used with sudo, the file command can also be used to read restricted information. While the file command usually takes files to identify from the command line, it can also take them from a file, where each line will be interpreted as a file to be read. If one of these files cannot be read, an error is output. These error messages will allow one to read a file.

## Privilege Escalation Using the Less Command

The less command usually allows users to break content down into pages. However, it has a lesser-known ability to run commands as well.

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2006.png)

For example, I use sudo with the less command to view /etc/passwd since it is large enough to be broken down into different pages (sudo less /etc/passwd).

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2007.png)

Being allowed to enumerate user and system accounts is an issue within itself, but now let’s dive into the even bigger problem this enables. While viewing the contents of /etc/passwd, I execute the !/bin/sh command, which spawns a shell and places me into the actual root account!

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2008.png)

I verify this by running the id command, which returns user and group information affirming my identity as root. To begin properly configuring the sudoers file to eliminate these vulnerabilities, I run the visudo command while still logged in as root.

![Image](https://github.com/darylcbrooks/project-1/blob/project-37/Project%2037%20-%20Step%2009.png)

Scrolling down to the very bottom of the sudoer configuration file, I notice that the admin account has the ability to run any command as any user using sudo. I also see the “no password” rule that allowed me to run less, base64, and file commands without the need to authenticate. I remove this rule as the first step to properly harden this attack vector.

## Conclusion

The purpose of sudo is to allow privileged Linux users to run administrator-level commands without compromising the integrity of the operating system. However, without the requirement of authentication, sudo becomes an attack vector instead of a security control. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
