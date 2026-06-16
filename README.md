### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/jzJZlP6BqCU/maxresdefault.jpg)](https://youtu.be/jzJZlP6BqCU)

### [Project 46 - Reverse Engineering Malware using Cyber Chef and Yara Rules 🦠](https://youtu.be/jzJZlP6BqCU)

In this project, I’ll be deconstructing a piece of malware using various tools to gain a better understanding of how it works. The more we understand how malicious code operates, the better we can defend against it.

## Static Code Analysis with Visual Studio Code

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2001.png)

To initiate the OpenVAS application from the Linux CLI, I run the following command: “sudo gvm-start.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2002.png)

Next, I minimize the PowerShell terminal to view my Desktop, where I can see the newly created crypto.txt file. I right‑click the document and choose the option to open it with Visual Studio Code.

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2003.png)

Once opened, I scroll through the captured strings until I find a portion encoded in Base64. I highlight and copy this section of text.

## Reverse Engineering the Malware with CyberChef

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2004.png)

I now open my web browser and navigate to https://cyberchef.org. CyberChef is a versatile web application designed for encrypting, decrypting, converting data, and more. It’s widely used in cybersecurity and digital forensics to analyze and decode data efficiently. I double‑click the “From Base64” option from the left‑hand menu and then paste the copied encoded text into the input pane on the right. In the output pane, a legible string appears that reads “Server is listen on port.” This indicates the presence of code designed to open a system port and wait for incoming connections.

## Writing a YARA Rule

In this final phase of the project, I will write a YARA rule to identify the string within the malware file that is associated with opening a port. YARA rules are specialized blueprints used in cybersecurity to identify and classify malware. They work by scanning files, processes, or memory for specific textual strings, hexadecimal byte patterns, or regular expressions, and triggering an alert when a defined logical condition is met.

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2005.png)

I type “run” in the taskbar search menu and enter “notepad.exe” to open the Notepad application.

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2006.png)

I write the following rule within Notepad:

rule PortOpeningDetection {

meta:

description = “Identifies malware attempting to open a system port”

strings:

$openPortString1 = “PT0=”

$openPortString2 = “MDNQT3Q1ZldhOEc3b3FKaQ==”

$openPortString5 = “MTIyN”

condition:

all of them

}

This rule aims to identify attempts to open a port by searching for specific strings that indicate such behavior.

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2007.png)

To save the new YARA rule, I click “File” and choose to save the file as “portopening.yar” on my Desktop.

![Image](https://github.com/darylcbrooks/project-1/blob/project-46/Project%2046%20-%20Step%2008.png)

Back in the PowerShell terminal, I run the following command to scan the crypto.exe file using the YARA rule I just created. We can now see the specific string in the code that is responsible for opening closed ports to listen for connections.

## Conclusion

These are just a few methods and tools that can be used to reverse engineer malicious code. In addition to using our findings to define indicators for automated solutions (IDS, IPS, SIEMs, etc.), we can also share this threat intelligence with industry partners to help reduce the prevalence of exploitation. You can watch the video walkthrough of this project at the top of the page. Until next time, take care of yourself and… SHOW YOUR WORK!!! 😉
