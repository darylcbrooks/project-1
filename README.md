### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/c5afEKoY-gs/0.jpg)](https://youtu.be/c5afEKoY-gs)

### [Project 32 - Automating Security Scans: Using Lynis Auditing Software and OpenVAS](https://youtu.be/c5afEKoY-gs)

The primary focus of this project will be to demonstrate how vulnerability scanning can be automated through the use of scheduled tasks. I’ll be using both Lynis Auditing Software and the OpenVAS open-source scanner to provide examples of this.

## Running and Scheduling Security Scans w/ Lynis

Lynis is an open-source security auditing tool designed to perform comprehensive security scans on Unix-based systems. It’s widely used for system hardening, vulnerability detection, and compliance testing.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2001.png)

Before attempting to schedule an automated Lynis scan of a system, I want to demonstrate how to run it manually and review its output first. To get started, I establish an SSH connection with the system at 192.168.1.100 (“ssh ubuntu-user@192.168.1.100”). This will be the target of my manual Lynis scan.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2002.png)

To initiate a basic security audit of the target system, I execute the following command and provide my user credentials to proceed: “sudo lynis audit system.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2003.png)

The system audit reveals important information about the system’s security posture, including various misconfigurations and vulnerabilities. Lynis makes a total of 53 suggestions on how to harden the system’s attack surface.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2004.png)

Now that we’ve seen what a basic Lynis system audit looks like, I’m going to create an automated task (cron job) to have the target system scanned on a schedule at regular intervals. Since I’m using a Linux-based operating system, I need to edit the crontab file to do so (“sudo crontab -e”). From the options provided, I select Nano as my text editor of choice to open the file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2005.png)

Once inside, I add the following string to the bottom of the crontab file:
“0 3 * * * /usr/sbin/lynis audit system > /var/log/lynis-$(date +%Y-%m-%d).log 2>&1.”

This instructs the Linux operating system to automatically run a Lynis scan every day at 3 a.m. To save the changes, I press Ctrl+X, hit Y, and then press Enter.

## Creating Scheduled Vulnerability Scans In OpenVAS

Security scans can also be automated in GUI-based vulnerability assessment tools such as OpenVAS.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2006.png)

To initiate the OpenVAS tool, I click on the Kali menu in the upper-left corner of the screen, type “start” in the search bar, and select the “gvm start” menu option that appears. Alternatively, I could simply execute the “sudo gvm-start” command in the terminal.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2007.png)

This opens a terminal window (with the “sudo gvm-start” command already executed), prompting me to enter my user credentials to proceed to the OpenVAS interface.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2008.png)

This launches an OpenVAS login screen in my browser, where I enter administrator credentials to continue to the dashboard.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2009.png)

The first set of specifications I need to provide to OpenVAS relates to details about our target system. To do so, I click the Configuration menu at the top of the page and select the “Targets” menu item.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2010.png)

On the next page, I click the small document icon in the upper-left corner of the window to open the menu for entering the necessary details. I name the target “Target 1,” specify the host as “192.168.1.100,” and set the port range to “All TCP and Nmap top 10.” I leave all other options at their default settings and click the green “Save” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2011.png)

With the target system details saved, I can begin building the scanning schedule. I return to the Configuration menu and select the “Schedules” menu item.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2012.png)

As before, I click the small document icon in the upper-left corner to open the details menu. I name the schedule “Time 1,” set the first scan to run at 2:30 p.m., and configure a custom recurrence for every Monday at the same time. I leave all other options at their defaults and click the green “Save” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2013.png)

Now that I’ve built the vulnerability scan schedule, I want to test its functionality. To create the necessary task, I go to the Scan menu at the top of the page and select the “Tasks” menu item.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2014.png)

I click the document icon in the upper-left corner to open the details menu. I name the new task “Scan Target 1,” select the “Target 1” entry created earlier, choose the “Time 1” schedule, and set the scanner to look for vulnerabilities based on documented CVEs. I leave all other options at their defaults and click the green “Save” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2015.png)

I initiate the scan by clicking the play button next to the “Scan Target 1” task. I wait a few minutes for the scan to begin, since it’s almost, but not quite 2:30 p.m. yet.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2016.png)

Once the scan completes, we can see that a CVE with a high severity rating of 9.8 was found on the system. To view the details of this and any other discovered vulnerabilities, I return to the Scan menu at the top of the page and select the “Reports” option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-32/Project%2032%20-%20Step%2017.png)

Clicking on the “Results” tab provides a full list of the specific CVEs found on the target system and their severity ratings, listed from highest to lowest. This helps cybersecurity specialists determine which vulnerabilities to prioritize, though an independent analysis should always be conducted as well.

## Conclusion

These were just a few examples of how simple cybersecurity tasks can be automated to reduce the amount of work involved in a vulnerability assessment. This gives professionals more time to plan and execute remediation measures on any discovered vulnerabilities while avoiding productivity disruptions by scanning outside peak hours of network usage. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
