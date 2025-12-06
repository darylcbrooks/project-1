### Click Header Image or Project Title to Watch The Video

[![Watch the video](https://img.youtube.com/vi/doYAROHx1gg/0.jpg)](https://www.youtube.com/watch?v=doYAROHx1gg)

### [Project 02 - Navigating with the Linux & Windows Command Line Interface (CLI)](https://youtu.be/doYAROHx1gg)

Welcome back! This is the official walkthrough of my second project covering some very basic commands to help tech professionals navigate both the Windows and Linux command line interfaces. While on the surface this topic may seem fundamental at best, its vital information to know as a Cybersecurity analyst. Whether we’re working as a blue team defense analyst or a red team penetration tester, we must know these commands and what it is that they do. If I’m conducting log analysis and see these commands used, then it will give me a better understanding of how a potential intruder made their way into our enterprise and what actions that may have taken next. On the other hand, if I’m conducting a penetration test, these basic commands will help me to enumerate the target host and look for directories to hide payloads in. Most of this project focuses on Linux based commands since most distributions of the operating systems heavily rely on extensive knowledge of its CLI vs Windows which relies more on its user friendly, point-and-click GUI. Nevertheless, both operating systems’ set of commands will be covered.

### [*Navigating Linux Directories*]

The “pwd” command allows users to see what their current working directory is. In the above example, I am currently in the “/home/kali” directory.


The “cd” command (short for change directory) is used to change to a different directory in the operating system. The proper format of the command is “cd <desired directory path>. In the image above, I’ve changed to the “/usr/bin” subdirectory and verified my current whereabouts by using the “pwd” command.

### [*Viewing Directory Contents*]

Press enter or click to view image in full size

The “ls” command (short for list) will display all files and subdirectories within the current working directory. I used the “ls” command in the above example and this very extensive list of files and subdirectories was returned back to me.

Press enter or click to view image in full size

The “ls -l” command will list all files and subdirectories within the current working directory including associated permissions, file size and modification dates. Above, these attributes can be seen in the aforementioned respective order.

Press enter or click to view image in full size

The “ls -lt” performs the same functions as the “ls -l” command but sorts the directory contents by modification dates and times (from recent to oldest). In the above image, we can see that the most recently modified file was last changed on January 23rd 2023 and the oldest modified file was last changed on December 18th 2022.

Press enter or click to view image in full size

The “ls -lh” command behaves similarly to the last 2 commands mentioned with the exception that it returns the file sizes of the directory contents in a more human readable format. The highlighted portion of the image are the sizes of the respective files.

### [*Shortcut Commands*]

The “cd ~” can be use to automatically take a user from their current working directory back to their home directory. It can be seen in the image above that once I executed the “cd ~” command, I’m returned to my home directory.

Fun fact: ~ is pronounced “tilde”


To verify that I am indeed back in my home directory, I use the “pwd” command again which displays the home directory.

Press enter or click to view image in full size

Many Linux distributions are capable of autocompleting a written command. To give the best example of how this can be used, in the above image I write and execute an incomplete command (cat /etc/pas) which of course fails to process. I then write the same command out again but this time I tap the Tab key which autocompletes the “cat /etc/passwd” command and successfully executes it. This in turn returns to me a listing of all the user accounts on the device.

### [*Creating A New Linux Directory*]

The “mkdir” (short for make directory) command is used to create a new directory. The proper format is “mkdir <desired directory path>. In the image above, I’ve created a new subdirectory under my home directory called “/dir1” (full logical name “/home/kali/dir1) using the command “mkdir /home/kali/dir1”.

Press enter or click to view image in full size

In order to verify the creation of the new subdirectory, I use the “ls” command again to display all the contents of my home directory, and we can see that the third to last entry is indeed the “dir1” subdirectory.

Press enter or click to view image in full size

In addition to the newly created subdirectory, we can also view all hidden files within my home directory by using the “ls -la” command. Observing the above image, Kali returns back a list of all the hidden files within my home directory. Hidden files can be easily identified by the “.” or period in front of the file name.

### [*Switching To The Root Directory*]

The root directory takes us a layer back beyond our home directory and allows us to view system level directories. The command to take us there is fairly simple: “cd /”. If we’re switching from the home directory to the root directory (as seen above), the change can be subtle. When in the home directory, the user account name is follow by a “~” or tilde character but when in the root directory, the character changes to a “/” or forward slash.


As usual, we can verify that we are in the root directory by using the “pwd” command. It will simply return a forward slash, indicating that the root directory is the current working directory.


Changing subdirectories in the root directory works the same as any other. Simply type “cd <desired directory path>”. In the above example, I switch to the /var subdirectory by using and executing the “cd /var” command.


To verify that I’m in the /root/var directory, I use and execute the “pwd” command which confirms that I’m in the desired directory.

### [*Miscellaneous Linux Commands*]

If we have multiple command terminals in use and want to know which one we are currently using, the “tty” command will provide us with that information. It can be seen in the above example that the terminal that I’m logged into is “/dev/pts/1”.


To view which user account is currently in use, the “whoami” command can be used. Above, we can see that my username is simply “kali”.

Press enter or click to view image in full size

The “w” command provides details about all logged in users including: current system time, system uptime, the number of logged in users, and system load averages. See the above image for visual details.

### [*Basic Windows Commands*]

Press enter or click to view image in full size

In order to open the Windows command prompt terminal, type “cmd” in the system tray’s search bar and the command prompt application should appear on the menu.


To view the contents of the current working directory, use the “dir” command.


If we want to switch to the current disk drive’s root directory, the command is exactly the same as it is in Linux: “cd /”. In the screenshot, the disk drive in use is the C drive and we can see that I’m taken back to its root upon execution of the “cd /” command.

Press enter or click to view image in full size

To view any hidden files or directories within the current working directory, use and execute the “dir /ah” command.


To view which Windows user account is currently in use, the “echo %username%” command can be used. Above, we can see that my username is “LabUser”.

### [*Conclusion*]

This wraps up project 2! Even though this information may be very basic to some, I hope that was insightful to many others. You can watch the video walkthrough of this same lab by clicking the link below. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉

