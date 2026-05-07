### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/veXF9fXKgiQ/maxresdefault.jpg)](https://youtu.be/veXF9fXKgiQ)

### [Project 39 - Isolating Compromised Users and Hardening Active Directory Security 🛡️](https://youtu.be/veXF9fXKgiQ)

Scenario: As a Windows system administrator working for "V Corp", senior SOC staff have provided me with details in regards to a detected insider threat collaborating with the well-known APT group, better known as "The Boys".  Though the attempted network breach has been contained, I'm tasked with removing the originating workstation from the enterprise network to prevent any further damage and to investigate Active Directory for any vulnerabilities that require immediate remediation. However, I must hurry as our super board of directors, "The Seven" expect a full report including implemented remediation measures by their next meeting!

In this project, I’ll be demonstrating how a system administrator can isolate a compromised workstation and user account from a simulated enterprise and harden Windows Active Directory settings to prevent future attacks.

## Manually Isolating an Infected Workstation From the Network

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2001.png)

To open the Active Directory menu, I type “Active Directory Users and Computers” into the Start menu’s search bar and select the respective menu item that appears.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2002.png)

I expand the “local.vcorp.com” menu and click the “Users” folder to view the full list of enterprise users. According to the intel I received, the user account associated with the network breach belongs to an “Alex Turner.” I search for their name in the list of users, right‑click it once I find it, and choose the option to disable the account.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2003.png)

Next, I move a few folders up from “Users” and select the “Computers” folder. Within the right‑most pane, I see “ALEX-PC,” which is the workstation belonging to Alex Turner. I right‑click the workstation name, choose the Properties option, and enter the “Dial‑in” tab of the resulting menu. Under the “Network Access Permission” section, I choose the “Deny access” option, click the “Apply” button at the bottom, and then the “OK” button to continue. This prevents Alex Turner’s workstation from accessing the enterprise network, isolating the device.

## Hardening Active Directory

In this phase of the project, I’ll be using Group Policies to enforce audit settings across all domain‑joined devices.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2004.png)

I start by opening the Windows Command Prompt by typing “cmd” into the Start menu’s search bar.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2005.png)

The “gpmc.msc” command will open the Group Policy Management Console.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2006.png)

Once the GPM console appears, I expand the “Forest: local.vcorp.com” menu in the left‑most pane, then expand the “Domains” menu, and finally expand the “local.vcorp.com” menu. I right‑click the “Default Domain Policy” item and choose “Edit.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2007.png)

This opens the Group Policy Management Editor console. I expand the following menus: “Computer Configuration,” “Policies,” “Windows Settings,” “Security Settings,” and “Advanced Audit Policy Configuration.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2008.png)

Clicking on “Audit Policies” within the “Advanced Audit Policy Configuration” menu allows me to see the status of each of Active Directory’s audit policies. It appears that none have been configured, which is a major contributor to the recent breach. Failing to audit the enterprise’s security posture at regular intervals can leave it vulnerable to breaches without immediate notification. Starting from top to bottom, the first audit policy I’ll configure is the “Account Logon” policy.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2009.png)

After double‑clicking on it, a list of subcategories associated with the policy appears. I highlight all four of them, right‑click the selection, and choose the Properties option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2010.png)

The resulting menu provides two options for the types of logon events I want audited. I check the first box and then both the “Audit all successes” and “Audit all failures” options. This will create a log of all login attempts for user accounts across the enterprise, making it easier to detect threats such as brute‑force dictionary attacks. I click the “Apply” and “OK” buttons to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2011.png)

I repeat the same process for the following audit policies: “Account Management,” “Detailed Tracking,” “DS Access,” “Logon/Logoff,” “Object Access,” “Policy Change,” “Privilege Use,” and “System.” This ensures that all relevant events are logged in the Windows Event Viewer.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2012.png)

To apply the updates made to Group Policy, I execute the “gpupdate /force” command in the Command Prompt terminal.

## Strengthening User Account Control Policies

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2013.png)

Back in the Group Policy Management Editor, under the “Security Settings” menu, I expand “Local Policies” and select “Security Options.” In the right‑most pane is a collection of security‑related policies that allow me to configure the behavior and restrictions for various aspects of the Windows operating system. These settings provide control over key system security features, user permissions, authentication, and access controls. Similar to the audit policies, I notice that most of these local security policies have not been defined. From this long list of policies, I will only configure a handful of them.

Under the “User Account Control” policies, I select the “Admin Approval Mode for the Built‑in Administrator Account” policy.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2014.png)

I check the “Define this policy setting” box and enable the policy. I click the “Apply” and “OK” buttons to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2015.png)

Next, I select the “Run all administrators in Admin Approval Mode” policy.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2016.png)

I check the “Define this policy setting” box and enable the policy. Again, I click “Apply” and “OK.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2017.png)

The next policy I select is “Behavior of the elevation prompt for administrators in Admin Approval Mode.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2018.png)

I check the “Define this policy setting” box and choose the “Prompt for credentials on the secure desktop” option from the dropdown menu. I click “Apply” and “OK” to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2019.png)

Finally, I select the “Behavior of the elevation prompt for standard users” policy.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2020.png)

I check the “Define this policy setting” box and choose the “Automatically deny elevation requests” option from the dropdown menu. I click “Apply” and “OK.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2021.png)

Back in the Command Prompt terminal, I run the “gpupdate /force” command again to apply the changes made to the local security policies.

## Configuring Inbound Firewall Rules

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2022.png)

Back in the Group Policy Management Editor, I move further down the list of options within the expanded “Security Settings” menu and expand the “Windows Defender Firewall with Advanced Security” option. I expand the “Windows Defender Firewall” menu, right‑click “Inbound Rules,” and choose “New Rule.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2023.png)

Within the resulting menu, I select the “Predefined” rule option and choose the “Active Directory Domain Services” item from the dropdown box. This includes all necessary rules for Active Directory services such as DNS, Kerberos, LDAP, and Active Directory replication.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2024.png)
![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2025.png)

I click “Next” through the remaining menu items, leaving the default settings selected, and click “Finish” to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2026.png)

To immediately apply these changes to the enterprise’s firewall rules, I run the “gpupdate /force” command again.

## Configuring and Using Windows Defender System Guard

Windows Defender System Guard is a security feature designed to maintain system integrity and protect against malicious attacks by leveraging both hardware and software defenses. It ensures that only trusted components are loaded into memory, protecting the system from kernel‑level exploits and maintaining the integrity of the boot process.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2027.png)

To access Windows Defender System Guard, I search for and select the “Settings” menu from the Windows Start menu.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2028.png)

On the following screen, I choose the “Update & Security” option.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2029.png)

Next, I select the “Windows Security” option in the left‑most pane and then “Device security,” which appears to the right.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2030.png)

On the next page, I click the “Core isolation details” link.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2031.png)

I then toggle the Memory Integrity option to the “On” position. Memory Integrity is a critical component that uses virtualization‑based security (VBS) to isolate sensitive processes from the rest of the operating system. It creates a secure, virtualized environment, ensuring that malicious code cannot alter or access protected processes. This feature prevents kernel‑level exploits, ensuring the system’s runtime environment remains secure and uncompromised.

## Using The System File Check Tool (SFC) and DISM for Advanced Integrity Checks

SFC is a command‑line utility in Windows designed to scan and repair system files. It ensures the integrity of critical system files by checking for corruption or modifications.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2032.png)

In the Command Prompt terminal, I run the “sfc /scannow” command to initiate the System File Check tool. After several minutes, it reports that no integrity violations were found in any of the system files.

![Image](https://github.com/darylcbrooks/project-1/blob/project-39/Project%2039%20-%20Step%2033.png)

The Deployment Image Servicing and Management (DISM) scanner is a powerful tool used to repair and maintain Windows images, including those used for the operating system. I run the following command to initiate the scanner and check the health of the Windows image in use: “dism /Online /Cleanup-Image /CheckHealth.” DISM reports that no corrupted OS components were found.

## Conclusion

These are just a few post‑incident steps that system administrators can take to review and harden an enterprise’s attack surface. Already having security controls in place, such as recurring security audits, strong UAC policies, strong inbound firewall rules, and advanced Windows security settings, will greatly reduce the risk of being breached in the first place. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
