### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/91Qq0LP8Grk/maxresdefault.jpg)](https://youtu.be/91Qq0LP8Grk)

### [Project 09 - Exploiting Alternate Data Streams 🔒 ](https://youtu.be/91Qq0LP8Grk)

In this project, I’ll be demonstrating how alternative data streams can be exploited to hide potentially malicious executable files. Alternative data streams (ADS) are a Windows NTFS file system feature that allows a single file to contain multiple data streams beyond the primary visible content. ADS enables hiding information (such as metadata or malware) within a legitimate file, as these streams are invisible to Windows Explorer.

When a file is created in Windows, it has two streams of data: a data stream and a resource stream. The data stream is the data contained within the file, while the resource stream is the associated metadata (name, size, creation date…the data about the data). Let’s see what happens if someone is able to tamper with a resource stream.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2001.png)

To start things off, I open a Windows Command Prompt window, as I will be relying on this tool for the majority of the project. I also switch to the Desktop directory since this is where I’ll primarily be working (CLI command: cd Desktop).

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2002.png)

Next, I type the “notepad test.txt” command to open the Notepad application and create an empty text file named “test.txt.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2003.png)

Now I add some simple text (“Hello World”) to the document and save it to my Desktop.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2004.png)

I right‑click the “test.txt” document that’s saved on my Desktop and select the “Properties” option from the menu.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2005.png)

From the four tabs located at the top of the Properties menu, I select the “Details” tab. This represents the metadata/resource stream. We can see information such as file size, creation date, owner, etc.

## Hiding A File Within The Resource Stream

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2006.png)

Now that I’ve demonstrated how a resource stream is generated upon the normal creation of a file, let’s see how to create a file with another one hidden within it. First, I’m going to delete the previously created text file from my Desktop to start things off fresh (CLI command: del test.txt).

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2007.png)

Within the Command Prompt window, I type the following command to create a new version of the “test.txt” file using Notepad, but this time hiding another newly created file named “secret.txt” within it: “notepad test.txt:secret.txt.” I select “Yes” to the prompt that appears in order to create the document.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2008.png)

I add the text “This is hidden” to the document and save it to my Desktop.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2009.png)

I now click the “test.txt” document saved on my Desktop and as shown above, nothing, not even the text I added in the previous step, appears inside of it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2010.png)

Back in the Command Prompt window, I use the “dir” command to see the details of all the files present within my current working directory. Only “test.txt” appears to be present (no “secret.txt” file) with a file size of zero. Windows Explorer is not only unable to detect the “secret.txt” file that was created with “test.txt,” but it also isn’t attributing the size of the data stored inside of it to “test.txt,” which it’s attached to!

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2011.png)

To prove my point, I add the text “This is visible” to “test.txt,” save it, close it, and reopen it to show that the newly written message persists.

![Image](https://github.com/darylcbrooks/project-1/blob/project-9/Project%209%20-%20Step%2012.png)

To view “secret.txt” and its contents — now well hidden in a resource stream — I type the following command in the Command Prompt window: “notepad test.txt:secret.txt.”

## Conclusion

From this demonstration, we can see that it’s fairly easy for someone to hide files (malicious or benign) in a resource stream. To prevent files from being hidden in an NTFS Alternate Data Stream, using PowerShell commands like “Get-Item -Stream *” and “Remove-Item -Stream” can help detect and remove them. Other methods include using third-party tools such as EventSentry or Sysinternals Streams, using the “dir /r” CLI command to view them, and ensuring security software is set to scan for ADS. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
