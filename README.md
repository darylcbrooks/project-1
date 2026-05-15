### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/5HNo6dLb7EU/maxresdefault.jpg)](https://youtu.be/5HNo6dLb7EU)

### [Project 45 - Remediating Vulnerabilities Found with OpenVAS and Nessus Scanners 🔍](https://youtu.be/5HNo6dLb7EU)

In this project, I will be conducting a mock vulnerability “grey box” assessment. A grey box security assessment is when a client provides analysts with some system information about their target. In this instance, I know the operating system and login credentials of my target host (192.168.100.202). First, I’ll use the OpenVAS scanner on the target address and then verify those results by running another scan against the same IP address using the Nessus vulnerability scanner.

## Starting the Initial Vulnerability Scan with OpenVAS

OpenVAS is a free and open-source vulnerability scanning tool used by security professionals to actively scan networks, servers, and applications for security weaknesses, misconfigurations, and outdated software before attackers can exploit them.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2001.png)

To initiate the OpenVAS application from the Linux CLI, I run the following command: “sudo gvm-start.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2002.png)

This automatically launches the hosted OpenVAS instance within my web browser. I provide my user credentials to log in.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2003.png)

At the top of the window, I click the “Configuration” menu and choose the “Target” option to begin entering the information about the host I’ll be scanning.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2004.png)

I click the small paper icon with an asterisk in the upper left corner of the window to bring up the menu I need. I specify the name of the target as “Target” and include the IP address next to the selected “Manual” radio button in the “Hosts” section (192.168.100.202). After providing all the necessary information, I click the green “Save” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2005.png)

At the top of the screen, I click the “Scans” menu and choose the “Tasks” option from the dropdown.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2006.png)

As before, I click the small paper icon with an asterisk in the leftmost corner to bring up a menu. In this menu, I name my scan “Windows Scan,” and the Scan Target is already preset to “Target.” I leave all other options set to default and click the green “Save” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2007.png)

At the bottom of the page, I can see the “Windows Scan” I just created. To initiate it, I click the play button under the “Actions” column. The scan takes approximately seven minutes to complete, so I’ll skip ahead to the results.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2008.png)

To view the reports, I go back to the “Scan” menu and choose the “Report” option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2009.png)

Notice that port 135 was found to have a vulnerability with a medium-severity CVSS score. “DCE/RPC and MSRPC Services Enumeration Reporting” refers to the Microsoft Remote Procedure Call, which the SMB protocol often serves as a transport mechanism for. This vulnerability can be problematic because RPC can be used as a method for a program to call a function in another process on a different machine.

## Performing a Nessus Scan

It’s always best practice to use multiple tools to compare and contrast results, reducing the possibility of acting on false positives and minimizing single points of failure. I will be scanning the target host again with the Nessus vulnerability scanner. Nessus is an industry-standard, proprietary vulnerability scanner developed by Tenable. It is widely used by cybersecurity professionals and IT teams to proactively scan networks, devices, and applications for security flaws, missing patches, and misconfigurations before they can be exploited.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2010.png)

I switch to the Windows machine hosting Nessus and navigate to https://localhost:8834 in my Microsoft Edge browser. This brings me to a login page where I enter my user credentials.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2011.png)

Once the dashboard loads, I click the “New Scan” button in the upper right-hand corner.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2012.png)

A list of scanning templates appears, and I choose the “Advanced Scan” template.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2013.png)

In the “Settings” tab, I name the scan “Vulnerability Scan” and specify the target IP address as 192.168.100.202.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2014.png)

In the “Credentials” tab, I select the “Windows” option on the left to specify the operating system type and enter the user account credentials for the target host.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2015.png)

In the final tab, “Plugins,” I disable all plugins listed in the leftmost column except for “Misc.,” “Windows,” “Windows Microsoft Bulletins,” and “Windows User Management.” This configuration enables Nessus to perform a credentialed scan specifically searching for known SMB vulnerabilities in Windows platforms. I click the blue “Save” button to finish configuring the scan.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2016.png)

To initiate the “Vulnerability Scan,” I click the play button located to the far right of the scan name.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2017.png)

After approximately ten minutes, the scan completes, and I click “Vulnerability Scan” to view the results. It appears that Nessus has found 26 vulnerabilities (as opposed to the two found by OpenVAS). Twenty-five are ranked low, while one has a medium rating. To see the details, I click the “Vulnerabilities” tab in the results menu.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2018.png)

The vulnerabilities are grouped into seven different families. I prioritize the highest-rated vulnerability, which has been categorized as “Miscellaneous” and identified as “SMB Signing Not Required.” I click on it for more information.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2019.png)

The next page provides details about the SMB-related vulnerability. Windows not requiring SMB signing is problematic because it provides no assurance that network communications are encrypted or protected against tampering.

## Searching For and Fixing the Misconfiguration on the Target Host

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2020.png)

I log directly into the target host to see if I can find the misconfiguration identified by Nessus. To do this, I need to access the Microsoft Common Console Document. I type “gpedit.msc” into the Start menu search bar and select the file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2021.png)

Once the Local Group Policy Editor appears, I expand the following menus to access the Security Options:

Computer Configuration > Windows Settings > Security Settings > Local Policies.

In the Policy pane, I navigate to the “Microsoft network server: Digitally sign communications (always)” policy and see that Nessus was correct — it is disabled. I double-click the policy to begin enabling it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-45/Project%2045%20-%20Step%2022.png)

In the resulting menu, I select the “Enabled” option, click the “Apply” button for the change to take effect immediately, and confirm the change by selecting “Yes” in the confirmation pop-up. Clicking “OK” exits the menu and finalizes the remediation of the vulnerability.

## Conclusion

As previously stated, running multiple scans with different tools can produce a wider range of results. In this instance, I was able to find an SMB-related vulnerability with Nessus that wasn’t discovered in my initial scan using OpenVAS. From here, I would run the Nessus scanner again to verify that the vulnerability is no longer present and then seek to remediate the RPC-related one found by OpenVAS. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
