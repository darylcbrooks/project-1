### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/gC9WSgAF4Jw/0.jpg)](https://youtu.be/gC9WSgAF4Jw)

### [Project 13 - Scripting for Cybersecurity Tasks 🖥️ ](https://youtu.be/gC9WSgAF4Jw)

In this project, I will be demonstrating practical ways scripting is used in cybersecurity. The two languages that I will be utilizing are Python and Bash. Using these languages can help automate simple tasks and reduce the time it takes for an analyst to complete some of the redundant tasks associated with their role.

### Analyzing A Custom Python-Based Port Scanning Script

Python is a versatile interpreted programming language known for its emphasis on readability, modularity, and code reuse. Its syntax is fairly simple, human-readable, and is one of the easier programming languages to learn. Let’s take a look at a custom Python-based port scanner to better understand how the language is leveraged to perform enumeration tasks.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2001.png)

In my Kali Linux terminal, I start by listing the contents of my current working directory (command “ls”) to locate the Python file I’ll be analyzing, port_scan.py.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2002.png)

In my Kali Linux terminal, I start by listing the contents of my current working directory (command “ls”) to locate the Python file I’ll be analyzing, port_scan.py.

After verifying that the file exists, I use the “cat” command followed by the filename to view its contents. We can see the syntax of the script in the image above, and I’m going to provide a line-by-line breakdown of how this port-scanning file works:

* import sys — The sys module provides functions and variables to interact with the Python runtime environment.
* import socket — The socket module maps to the BSD socket interface.
* sys.argv[ ] — This returns a list of command-line arguments passed to the script, which are, by default, strings.
* start_port = int(…) / finish_port = int(…) — The starting and ending ports are parsed as integers and stored in the start_port and finish_port variables. These values will later define the port range to be scanned.
* count_ports — This is initialized to store the number of open ports. All variables must be assigned an initial value upon declaration.
* for port in range(start_port, finish_port) — The range function requires integer values; passing any other type results in an error. It generates a sequence of numbers between two integers, corresponding to the port numbers to be scanned.

Each time a port is scanned within the integer range, the following loop occurs: a TCP socket is created; the socket attempts to connect to the target; a determination is made as to whether the port is open or closed; and finally, the socket is closed.

If the port is closed, the code inside the IF statement is skipped, the connection is closed, and the next port is tested.
If the port is open, a message displaying the open port number is printed, and the count_ports variable is incremented by 1.

Let’s run the scanner to observe the output of the script’s inner workings.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2003.png)

The command structure for executing the port scanner begins with the version of Python installed on my operating system — Python 3. I then specify the port scanner filename (port_scan.py), the IP address of the host I’m scanning (192.168.1.101), and the port range I want to analyze (ports 20 through 4000). The following command is used to execute the script:

“python3 port_scan.py 192.168.1.101 20 4000”

Based on the results, the script ran successfully. It detected that ports 21 (FTP), 22 (SSH), 25 (SMTP), 3306 (MySQL), and 3389 (RDP) were open on the target host. The results also displayed a completion message along with the total number of open ports found out of the total amount scanned.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2004.png)

Now that I’ve completed this phase of my project, I want to delete the Python script. To do this, I execute the “rm” command followed by the filename (“rm port_scan.py”). My operating system asks me to confirm the deletion, and I provide a “y” response.

### Creating A Bash Script to Ping a Targeted Host

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2005.png)

Now that I’ve analyzed how a custom script performs host enumeration, I want to demonstrate how another custom script can be created — this time using Bash. Bash scripts are text files containing a series of commands to perform a task. To begin, I create an empty shell script using the “touch” command and name the file ping.sh (“touch ping.sh”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2006.png)

I want this script to automate the process of pinging a host to verify if it’s online. The image above shows how to manually ping an IP address and specify that only one ICMP packet should be sent (“ping 192.168.1.101 -c 1”). The results confirm that one packet was sent, received, and that the host is online.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2007.png)

I then copy this command into the empty shell script I created (“echo ‘ping 192.168.1.101 -c 1’ > ping.sh”). In theory, all I need to do now is execute ping.sh to get the same result as manually writing out the command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2008.png)

To verify that the command was successfully copied, I execute “cat ping.sh”. The output confirms the file contains the command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2009.png)

Next, I attempt to execute the script by typing “./ping.sh”. I receive a message stating permission was denied. To resolve this, I need to modify the file permissions.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2010.png)

I use the “chmod” command, followed by the octal notation representing the permissions I want to assign, and then the filename (“chmod 744 ping.sh”). Without diving too deeply into octal notation, 744 grants the owner full permissions (read, write, execute), while the group and others have read-only access.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2011.png)

When I run the script again — SUCCESS! I get the same results as before.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2012.png)

Now I want to slightly modify how this script operates. To make changes, I open the file using the “nano” text editor (“nano ping.sh”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2013.png)

At the top of the script, I want to add a comment reminding myself and anyone else who views it that this script should be executed using the Bash interpreter. The “#!” characters signal the interpreter path (#!/bin/bash). I replace “ping 192.168.1.101 -c 1” with “ping $1 -c 1”. The $1 argument uses the first keyword after the filename as user input. I save and exit using Ctrl + X, then pressing Y and Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2014.png)

This modification allows the script to be used against any IP address specified at runtime, rather than always returning the status of 192.168.1.101. In the image above, we can see that I successfully executed the script using 192.168.1.101 as the external argument.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2015.png)

There’s one more feature I want to add. Upon a successful ping, I want the script to display a custom message indicating the IP address and confirming it is online. To do this, I reopen ping.sh with “nano ping.sh” and add the following lines:

if [ $? -eq 0 ];
    then echo "Host $1 is up!"
fi

This tells the script that if the return value of the previous command ($?) is 0, then it should output: “Host [value of $1] is up!”. A return value of 0 indicates success. I save and close the file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-13/Project%2013%20-%20Step%2016.png)

Testing the script again, we can see that the results are the same as before, but now the custom message “Host 192.168.1.101 is up!” appears at the end.

### Conclusion

These are just a few examples of how scripts can simplify and automate routine tasks. My Python script can be expanded to scan multiple IP addresses, and the Bash script can be modified to ping an entire subnet to identify active hosts. These enhancements can save a cybersecurity analyst valuable time when beginning any type of engagement. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
