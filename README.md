[![Watch the video](https://img.youtube.com/vi/FW1amhy92oU/maxresdefault.jpg)](https://youtu.be/FW1amhy92oU)

### [Project 01 - Essential Powershell Commands for System Administration and Enumeration](https://youtu.be/FW1amhy92oU)

PowerShell is a cross-platform command-line shell and scripting language developed by Microsoft. It is designed for task automation and configuration management across various operating systems, including Windows, Linux, and macOS. Powershell is a vital tool in the realms of system administration and cybersecurity. In this project, I will be walking through several steps that cover some very basic but essential Powershell commands that every IT technician should know to efficiently complete everyday tasks.

### [*Step 1: Get-Help*]

Press enter or click to view image in full size

Open Powershell and type “Get-Help”. This command is used in conjunction with other commands to display instructions on how to use them.

### [*Step 2: Get-Command*]

Press enter or click to view image in full size

Typing in and executing the “Get-Command” gives a full list of all possible Powershell commands. This is very helpful for technicians who may forget the exact syntax of commands or to know if they are unintentionally using a Linux based command and want to find out what the Windows variation of that is.

### [*Step 3: Get-Help -Name Process*]

Press enter or click to view image in full size

Executing the “Get-Help -Name Process” command provides help for specific processes (replace “-Name Process” with actual process name). The above image displays an example of the output received when using this command.

### [*Step 4: dir*]

Using the “dir” command will display the list of folders present in the current working directory.

### [*Step 5: $PSVersionTable*]

The “$PSVersionTable” command provides specifications on the current version of Powershell being ran. This helps with ensuring that the desired and most up to date version of the tool is running on the system.

### [*Step 6: Get-ExecutionPolicy*]

The “Get-ExecutionPolicy” command can be used to check the device’s execution policy. Microsoft typically has this disabled by default to prevent the execution and spread of malware. There are 4 execution policy types:

Restricted (the default execution policy) — it locks PowerShell down so that commands can be entered only interactively. PowerShell scripts will not be allowed to run.

All Signed — scripts will be allowed to run, but only if they are signed by a trusted publisher.

Remote Signed — any PowerShell scripts that have been created locally will be allowed to run. Scripts created from a remote source will only be allowed to run if they are signed by a trusted publisher.

Unrestricted — The are no restrictions placed on the execution policy to stop any executable files from running.

In this project, my execution policy is set to “Unrestricted”.

### [*Step 7: Get-History*]

Using the “Get-History” command returns back a list of all of the commands used during the current working session of Powershell. In the above image, we can see all the previous commands used up to this point of the project walkthrough.

### [*Step 8: Get-Process*]
Press enter or click to view image in full size

The “Get-Process” command will allow Powershell users to see a list of all running system services. This can help technicians verify that essential system services are running and/or cybersecurity analyst to find suspicious processes to further investigate.

### [*Step 9: Get-Location*]

Executing the “Get-Location” command will provide a display of the current working directory that the user is in.

### [*Step 10: File Creation*]

To create a new file from the Powershell command line, use the “New-Item” command with the name and format of the file. In this project, I created a new file named TestFile.txt by using the following command: “New-Item TestFile.txt”.

### [*Step 11: Adding Content To Files*]
Press enter or click to view image in full size

In order to add content to existing files, use the “Set-Content” command with the name of the file and then set the “-value” parameter to whatever input to be added to the file. To add the sentence “Infosec rocks!” to the my newly created “TestFile.txt” document, I used the following command: Set-Content TestFile.txt -value “Infosec is awesome!”.

### [*Step 12: Viewing File Content*]

To display the contents of a file within the Powershell terminal, use the “Get-Content” command with the name of the file and upon execution, the contents will be displayed. To view the sentence that I added in the previous step, I used the following command to display the “Infosec rocks!” sentence: “Get-Content TestFile.txt”

### [*Step 13: File Removal*]

If a file needs to be deleted from the current working directory, use the “Remove-Item” command along with the name of the file needing to be removed. In order to delete the “TestFile.txt” file from my local directory, I used the following command: “Remove-Item TestFile.txt”.

### [*Step 14: Verifying File Removal*]
Press enter or click to view image in full size

There are several ways that we can verify the removal of a file (i.e. looking directly into the current working directory from the command line or File Explorer GUI), in my case, I decided to run the “Get-Content TestFile.txt” command again to see what would happen. As anticipated, I received the above error message indicating the TestFile.txt no longer exists to retrieve information from.

These were but a few basic Powershell commands to know but nevertheless, I hope this walkthrough was insightful. Feel free to also view my video walkthrough of this project to see it “live and in color” and until next time, take care of yourself and….SHOW YOUR WORK! 😉
