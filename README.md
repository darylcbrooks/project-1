### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/ZwWQGkt0k8U/0.jpg)](https://youtu.be/ZwWQGkt0k8U)

### [Project 28 - Recovering from a Malware Infection: Step by Step Guide](https://youtu.be/ZwWQGkt0k8U)


The following project is based on a scenario where a Windows-based workstation has been compromised, resulting in several security settings being disabled. I will be demonstrating the steps that can be taken to restore the device to a workable state so it can be added back to the enterprise network.

## Re-Hardening the System’s Attack Surface

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2001.png)

Several security features were disabled during the recent security incident, and I will need to re-enable them. While most of these settings can be restored through the Windows GUI, I’m going to demonstrate how to re-enable them using PowerShell commands. First, I open the Windows Start menu, right-click the PowerShell icon, and choose to run it as an administrator. Elevated privileges are required to run most of these commands.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2002.png)

I use the following command to remove the “Disable Real Time Monitoring” registry key, which may have been set by malware to keep Windows Defender turned off:

Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" -Name "DisableRealtimeMonitoring" -ErrorAction Ignore

Since this modification occurred at the registry level, simply toggling Real Time Monitoring back on in Windows Defender may not have persisted.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2003.png)

After adjusting the registry value, I execute the following command to turn Real Time Detection back on:

Set-MpPreference -DisableRealtimeMonitoring $false

This is the PowerShell equivalent of toggling Real Time Monitoring on through the GUI.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2004.png)

With threat detection back online, I then enable the intrusion prevention system:

Set-MpPreference -DisableIntrusionPreventionSystem $false

This feature takes action against detected threats.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2005.png)

Next, I restore my ability to send Microsoft reports concerning threats:

Set-MpPreference -MAPSReporting 2

This helps improve their malware database.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2006.png)

I further expand on this by enabling the feature that allows sending actual malware samples to Microsoft for analysis:

Set-MpPreference -SubmitSamplesConsent 1

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2007.png)

Because Tamper Protection cannot be restored through PowerShell, I must re-enable it through the Windows GUI. To begin, I open the Start menu again and select Windows Security.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2008.png)

From the Windows Security menu, I choose Virus & threat protection.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2009.png)

On the next page, I select Manage settings under Virus & threat protection settings.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2010.png)

Here, I can see the Real Time Detection and Intrusion Prevention features that I previously enabled through PowerShell. Scrolling down, I locate the Tamper Protection feature, which is turned off. I toggle it on. As described above, Tamper Protection prevents malicious modifications to Windows security settings.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2011.png)

Next, I enable the firewall for all network profiles to enforce security policies and block unwanted traffic:

Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2012.png)

The final hardening technique I perform is a System File Checker scan. Running:

sfc /scannow

will scan for and repair any system files that may have been corrupted by malware. The scan takes time, but fortunately it didn’t detect any corrupted files.

## Restoring System Data From Backup

In this phase, I demonstrate the steps to restore the system to its last working state (or as close as possible) from a backup file. Because this project uses a virtual machine, I cannot fully replicate the entire process, but I can capture it up to a certain point and summarize the rest.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2013.png)

I start by opening the Windows Start menu, searching for the Control Panel, and selecting it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2014.png)

Under System and Security, I choose Backup and Restore.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2015.png)

Here is where I would select a backup file to restore the system from.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2016.png)

After choosing the backup file, I would continue following the on-screen instructions.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2017.png)

Eventually, I would be presented with a list of restore points and would select the last one from before the security incident.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2018.png)

From there, I would confirm the restoration and restart the system once it is complete.

## Applying Security Patches and Software Updates

Next, I check whether my operating system is up to date and download any patches or updates that may have been rolled back or excluded from the backup.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2019.png)

I open the Windows Start menu again and click Check for updates.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2020.png)

On the next screen, I can see that the system indeed needs updates. I click Check for updates to begin the process. Depending on how many patches were missed, this may take anywhere from a few minutes to several hours. In my case, it appears to be a lengthy installation.

## Reviewing Installed Applications for Outdated or Unknown Software

The last step in the system recovery process is checking for any remnants of potentially malicious or outdated software that could be vulnerable to exploitation.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2021.png)

Back in the Windows Start menu, I open the Control Panel again.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2022.png)

I navigate to the Programs section to view all installed software.

![Image](https://github.com/darylcbrooks/project-1/blob/project-28/Project%2028%20-%20Step%2023.png)

From this list, I determine whether any outdated, unused, or suspicious applications need to be uninstalled. After reviewing it, I determine that everything looks legitimate and up to date. Another recommended step is to review all plug-ins and third-party extensions used by installed web browsers to ensure they are safe and updated.

## Conclusion

These are just a few basic steps that can be taken to help restore a Windows-based system after a security incident and to harden its attack surface against future compromise. A few preventative measures that can be taken are:

* Regularly perform automated audits of network devices’ security posture and adherence to enterprise security policy
* Perform regular malware and vulnerability scans
* Perform regular backups that adhere to Recovery Point Objectives
* Regularly install operating system patches and updates
* Implement a whitelist or blacklist approval strategy for third-party software applications and browser extensions

You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
