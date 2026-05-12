### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/Gp8arI4cSpI/maxresdefault.jpg)](https://youtu.be/Gp8arI4cSpI)

### [ Project 42 - Enumerating User and System Information in Linux as a Sys Admin 🖥️ ](https://youtu.be/Gp8arI4cSpI)

There are many ways to gather user and system information on a Linux platform. In this project, I will demonstrate a few simple command-line utilities that enable system administrators to enumerate critical details about system information and users.

## Finding User and Group Information

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2001.png)

The “id” command can be used to display the current user’s UID (user identifier), username, GID (group identifier), group name, and all secondary groups the user is a member of. In the above example, we can see that the user’s UID and GID are “kali.” It also has membership in the kali, cdrom, floppy, sudo, audio, dip, video, plugdev, netdev, bluetooth, and scanner groups.

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2002.png)

If I wanted to display only the user’s group memberships, I would use the “groups” command. This omits the UID and GID returned by the “id” command.

## Displaying the History of Successful Login Attempts

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2003.png)

I can check to see when the last login attempts occurred on the system by executing the “busybox last” command. BusyBox is an implementation of many Unix commands in a single executable file. It essentially combines tiny versions of many common UNIX utilities into one small executable. The “last” command reports the history of successful user login attempts and system reboots by checking the /var/log/wtmp file. The /var/log directory is used to store information about all login attempts, logins, logouts, and system reboots.

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2004.png)

The following example shows the output of this command, displaying my system’s login, reboot, and logout history.

## Viewing System Information

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2005.png)

If I want to view basic information about my system, I’ll run the “uname” command. When running the command, mine simply returns an output of “Linux,” which is the operating system type in use. There are several options available with the “uname” command, such as -a (all information), -s (kernel name), -n (node name), and -r (kernel release).

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2006.png)

Running “uname” with the “-a” option provides a much more verbose amount of system information, including the distribution type and version of my Linux operating system.

## Displaying System Uptime and Other Enumeration Commands

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2007.png)

Using the “uptime” command shows the system’s current time, how long it has been running, the number of users currently logged in, and the average number of processes over the past one, five, and fifteen minutes.

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2008.png)

When run with other commands, “which” shows where the respective command will execute if a direct path is not specified on the command line. For example, I ran the “which” command with “cat” without specifying a path, and the output displayed that the “cat” command will be executed from the “/usr/bin” directory by default.

![Image](https://github.com/darylcbrooks/project-1/blob/project-42/Project%2042%20-%20Step%2009.png)

The “wc” command displays the number of lines, words, and characters (or bytes) contained in a text file or input supplied. In the example above, I used several different options against the “profile” file to demonstrate how “wc” works:

* Using the “-l” option returns a line count for the file, which contains 34 lines.
* Using the “-w” option prints a total word count of the file, which contains 111 words.
* Using the “-c” option displays the total byte count of the file, which is 769 bytes.
* Using the “-m” option outputs the total number of characters in the file, which is 769 characters.

## Conclusion
While these are simple and efficient command-line utilities to gather information on the current state of a Linux system, an unauthorized user would possess the same ability to run them as well. This project serves as both a resource and a warning about the ways system users can enumerate the condition of a Linux system for their own purposes. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
