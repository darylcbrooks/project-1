### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/wLkS5D6QSxY/0.jpg)](https://youtu.be/wLkS5D6QSxY)

### [Project 30 - Extracting and Analyzing Malicious Traffic with T Shark](https://youtu.be/wLkS5D6QSxY)

This project will be a follow-up to a previous one where I covered basic functions of the TShark packet analysis tool. This time around, I’ll be demonstrating more advanced usage for TShark and how it can be an essential asset to a cybersecurity analyst.

## Filtering Network Traffic

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2001.png)

The first thing that I need to do is switch to the directory where my pcap (packet capture) files are located, which is the “pcap_files” folder (“cd Desktop/Files/pcap_files”). Once there, I use the “ls” command to list the contents of the pcap_files directory.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2002.png)

As we can see in the above image, there are quite a few files stored in this directory. I will be using TShark to interact with the “http.cap” file (not pictured).

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2003.png)

Next, I run the following command to have TShark filter the traffic capture file for only HTTP-related packets: “tshark -r http.cap -Y http”. It appears that four HTTP packets are present in the captured network traffic.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2004.png)

However, I only want to view packets that are actually carrying files. To filter the traffic further, I execute the following command: “tshark -r http.cap -Y http.content_type”. In the above image, we can see that packet 27 may contain HTML webpage data.

## Viewing Data For Specified Packets

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2005.png)

The following command will print a subset of data using the “-V” flag in TShark: “tshark -r http.cap -V -Y frame.number==27”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2006.png)

In the output, I can only see part of the HTML webpage included in the packet. In addition to this limitation, I can only see usable data since HTML is a text‑based language.

## Performing A Packet Hex Dump

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2007.png)

The “tshark -r http.cap -x -Y frame.number==27” command returns a partial hex dump for packet 27 in the http.cap traffic capture. By printing the entire HTTP download session, filtering out packet headers, and converting hex characters to raw bits, a full file can be extracted.

## Exporting Objects

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2008.png)

To view a list of supported export protocols, I execute the “tshark -r http.cap -export-objects .” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2009.png)

Since I’m dealing with HTTP packets, I choose to use the http option to dump HTTP objects from the http.cap traffic capture file into a directory named “dumped” (“tshark -r http.cap -q -export-objects http,./dumped”). The “-q” flag suppresses TShark’s usual output, which would print the summary information from all packets.

## Finding Packets of Interest

During this phase of the project, I will be working with a different packet capture file, multistage.pcap. The objective will be to identify malicious traffic believed to be responsible for a recent malware infection of an enterprise user’s workstation.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2010.png)

The following command will identify potentially interesting files and filter the content based on HTTP content type: “tshark -r multistage.pcap -Y http.content_type”. The packet we’re observing in the multistage.pcap file includes traffic captures from a multi‑stage malware infection. Packet 323 contains a Microsoft Word file, which could include macros or other functionality that acts as a downloader for future stages. Let’s extract the file!

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2011.png)

To view the details of packet 323, I use the following command: “tshark -r multistage.pcap -V -Y frame.number==323”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2012.png)

Looking at Content-Disposition, I can see the name of the file I’m searching for is USPS_invoice_reggie.cage.doc, and further down in Request URI, it comes from the margo.co.jp domain. Also, looking a little further up at Content-length, I can see that the file is apparently 204,288 bytes long. This is indicative of it being broken up into several packets.

## Identifying And Viewing A Specific TCP Stream

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2013.png)

Using the following command allows me to directly search the contents of multistage.pcap for the index number of the TCP stream associated with packet 323: “tshark -r multistage.pcap -V -Y frame.number==323 | grep Stream”. The stream index number is 0.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2014.png)

I follow this up with a command that will display the contents of TCP stream 0 (“tshark -q -r multistage.pcap -z follow,tcp,ascii,0 | head -100”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2015.png)

The output isn’t human‑readable, so unfortunately, it doesn’t provide any further insight about the MS Word document.

## Chaining Commands to Dump The PCAP File

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2016.png)

I combine the following two commands together: “tshark -r multistage.pcap -w — -Y frame.number==323 | tshark -r -”. The first command sets permission to write to standard output, and the second command sets permission to read from standard input.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2017.png)

Next, I chain two more commands together to first filter out TCP stream 0 and save a file to the “docs” directory. TShark pulls the original file name and not what it was renamed to (<b>original name</b>: get.php%3fid=cmVnZ2llLmNhZ2VAeWFob28uY29t, <b>renamed to</b>: USPS_invoice_reggie.cage.doc).

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2018.png)

I use the “cd” command along with the folder name to switch to the “docs” directory (“cd docs”). Then I use the “ls” command to confirm whether the original file was copied over to the directory and verify that it is. Now I display the contents of the extracted file (“more docs/get.php%3fid=cmVnZ2llLmNhZ2VAeWFob28uY29t”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2019.png)

There isn’t anything here that tells me much; however, I can see a couple of Microsoft Word references. This somewhat confirms the file’s association with USPS_invoice_reggie.cage.doc.

![Image](https://github.com/darylcbrooks/project-1/blob/project-30/Project%2030%20-%20Step%2020.png)

In an attempt to enumerate more details on get.php%3fid=cmVnZ2llLmNhZ2VAeWFob28uY29t, I run the following command: “file docs/get.php%3fid=cmVnZ2llLmNhZ2VAeWFob28uY29t”. The outputted metadata informs me that the file type is a document, it was created by someone named Tim using Microsoft Word, and it was created on February 6th, 2017 at 12:42 pm. These details would be escalated to senior analysts and incident response specialists for further review.

## Conclusion

The purpose of this project was to demonstrate TShark’s ability to filter captured traffic, display packet details, and extract potentially malicious files using the Linux CLI. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
