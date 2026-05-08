### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/ksF0qI09LeE/maxresdefault.jpg)](https://youtu.be/ksF0qI09LeE)

### [Project 40 - Searching for Persistence: Incident Response Insights](https://youtu.be/ksF0qI09LeE)

In this project, I’ll be analyzing Windows System Monitoring (Sysmon) logs for indicators of persistence from previous network intrusions. Sysmon logs can provide valuable insights into suspicious system activities and help identify anomalies associated with persistence mechanisms, such as scheduled tasks, DLL injections, or registry modifications.

## Filtering Sysmon Logs in Windows Event Viewer

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2001.png)

I’ll begin by viewing the system monitoring logs within Windows’ Event Viewer tool. To access it, I type “eventvwr.msc” into the Start menu’s search bar and click the resulting icon.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2002.png)

Once Event Viewer has opened, in the left-most pane I expand the following folders: “Applications and Services,” “Microsoft,” and “Windows.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2003.png)

I scroll down the long list of subdirectories in the “Windows” folder, expand the “Sysmon” folder, and select the “Operational” events option to view the full list of Sysmon logs. Moving to the right-most pane (“Action”), I click “Filter Current Log” to open the filtering menu. In the “Logged” dropdown menu, I choose the “Custom range…” option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2004.png)

In the “Custom Range” submenu, I change the “From” and “To” dropdowns to “Events On,” set the date to 2/7/2025, and set the time to 12:00:00 AM to 11:59:59 PM. I then click the “OK” button to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2005.png)

Back in the “Filter Current Log” menu, I type “13” into the Event ID field to view only Sysmon logs related to Event 13 within the specified date and time. I click “OK” to proceed.

## Comparing and Analyzing the Sysmon Logs for Inconsistencies

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2006.png)

This displays the full list of Sysmon logs for Event 13 for the date 2/7/2025. I click on the first log in the list and see that it contains a registry key for a scheduled task for Windows Update. I take note of the following syntax:
“HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\Microsoft\Windows\WindowsUpdate\Scheduled Start\Index”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2007.png)

I exit that log entry and scroll down the list. When I select the log entry for 8:00:57 AM, I see that the folder name for “Windows Update” now includes a space between the words (…\Windows\Windows Update\Scheduled Start\Index). Also, the directory path that the scheduled task is running from is missing two folders that appeared before “WindowsUpdate” in the original directory (“Microsoft” and “Windows”), as well as one folder that appeared after “Windows” (“Scheduled Start”). The resulting path is:
“HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree\Windows Update\Index”.

## Investigating the Scheduled Tasks

Scheduled tasks are a common persistence mechanism attackers use to execute malicious code, maintain access, or escalate privileges. They can configure scheduled tasks to run malicious files at regular intervals, ensuring persistence while evading detection.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2008.png)

To open the Windows Task Scheduler, I type “taskschd.msc” into the Start menu’s search bar and click the icon that appears.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2009.png)

Within the Task Scheduler GUI, the bottom pane displays the suspicious Windows Update task, which is scheduled to run every day at noon. I double-click the task to view more details.

![Image](https://github.com/darylcbrooks/project-1/blob/project-40/Project%2040%20-%20Step%2010.png)

Tabs now appear in the bottom pane. I navigate to the “Action” tab and see that a DLL named “res6.a” is executed by the Windows Update task, which then triggers an export function called “StartA”. Dynamic Link Libraries (DLLs) are files used by Windows that contain code and data, allowing multiple programs to share functionality. Attackers often place DLL files in locations that are difficult to access or hidden within system directories to evade detection. These paths are strategically chosen to blend in with legitimate files or exploit areas where monitoring is less likely. Hiding and executing this DLL within a legitimate-looking Windows task is more than likely the mechanism a previous intruder used to maintain access to the network without being detected.

## Conclusion

There are a few ways that persistence mechanisms like this can be mitigated:

* Monitor sensitive directories such as the “Temp” and “Tasks” folders with tools like Wazuh and OSSEC.
* Block malicious domains and IPs.
* Actively enforce the principle of least privilege in the enterprise security policy.
* Deploy endpoint detection and response (EDR) solutions that can detect and alert on suspicious DLL executions, unexpected file creations in sensitive directories, and attempts at lateral movement.

You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
