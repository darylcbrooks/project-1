### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/ruHZc0C48cM/0.jpg)](https://youtu.be/ruHZc0C48cM)

### [Project 16 - Analyzing .pcap files for threats 🕵️ ](https://youtu.be/ruHZc0C48cM)

This will be another project focused on packet analysis. This time, I will start things off by using a command-line tool known as Zeek to analyze network traffic. To finish things off, I’ll use Wireshark again to investigate suspicious traffic patterns and attempt to identify the presence of any malicious payloads.

### Using Zeek to Hunt for Hidden Threats in PCAP files

Zeek is a free and open-source network analysis framework used to provide deep insight into network activity by generating detailed, high‑fidelity transaction logs of network data. Let’s jump into seeing how these logs are generated.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2001.png)


In the Kali Linux CLI, the first thing I do is switch to my local Desktop directory since that’s where the initial PCAP file that I’ll be generating logs from is stored (cd Desktop).

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2002.png)

Next, I use the zeek -r covertchannel.pcap command to create a variety of logs from the specified PCAP file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2003.png)

Observing the left side of the image above, we can see the logs that have been generated from the covertchannel.pcap file, and they have been named according to their contents (dns.log, files.log, http.log, conn.log, etc.).

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2004.png)

The first log that I want to view is conn.log. This is the connection log, and running the following command will allow me to view details such as all captured IP addresses, ports in use, and the duration of the connections:

zeek-cut < conn.log -d ts id.orig_h id.resp_h id.orig_p id.resp_p duration

In the screenshot, we see all the IP addresses that the local address, 192.168.153.129, connected to, over which ports, and for how long.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2005.png)

If I want to reduce the displayed output to just entries containing specific details — such as what connections were made over port 546 — I would use the following command:

zeek-cut < conn.log -d ts id.orig_h id.resp_h id.orig_p id.resp_p duration | grep 546

The grep 546 portion of the command tells Zeek to only provide output containing “546,” giving me log data relevant to that port.

Note: Port 546 is typically used by IPv6 addresses to communicate, which is why the IP address for this connection looks vastly different from the others in the initial conn.log output.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2006.png)

Before moving on, I want to view the http.log file to see if I can find any interesting content in it, since it does contain traffic going to and from the public internet. The following command returns GET and POST requests along with the URLs that were interacted with:

zeek-cut < http.log -d ts id.orig_h id.orig_p id.resp_h id.resp_p duration method uri

Observe the third entry in the image above — there appears to be an executable file named supercalc.exe that was interacted with at IP address 206.188.192.204. My next step is to investigate this further using a few additional tools to determine whether there is any cause for concern.

### Investigating The PCAP File w/Wireshark

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2007.png)

The first tool that I’m going to use for further analysis is Wireshark. I execute the wireshark covertchannel.pcap command to load the packet capture inside the Wireshark GUI.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2008.png)

Now that I have the full set of packets loaded, I want to narrow my view to only the interactions between the local address 192.168.153.129 and the external address 206.188.192.204 (the IP containing the supercalc.exe file). To do so, I enter the following filter into Wireshark’s filter bar:

ip.addr==192.168.153.129 && ip.addr==206.188.192.204

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2009.png)

Next, I click the File menu at the top of the Wireshark window, choose Export Objects, and select HTTP.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2010.png)

From the list of entries that appear, we can see that supercalc.exe is second on the list. I click on the entry and then click Save to store the suspicious file on my local Desktop.

### Uncovering The Executable File’s True Nature Using VirusTotal

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2011.png)

Now that I’ve managed to retrieve the executable file in question, I want to scan it with VirusTotal for any known malicious signatures. I open a browser and navigate to www.virustotal.com.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2012.png)

Under the File tab on the home screen, I click Choose File and select supercalc.exe for analysis.

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2013.png)

In a matter of seconds, VirusTotal returns sixty red flags from various vendors alerting me to supercalc.exe’s malicious nature (the majority flagging it as Trojan Horse malware).

![Image](https://github.com/darylcbrooks/project-1/blob/project-16/Project%2016%20-%20Step%2014.png)

There are other tabs adjacent to the initial Detection tab that provide more details about the file, but the most interesting information is under the Behavior tab. This tab gathers TTPs (Tactics, Techniques, and Procedures) from the MITRE ATT&CK framework — a structured knowledge base of adversarial behaviors that maps the “why,” “how,” and specific actions used during cyberattacks. These details are especially important to senior analysts and incident response specialists once the case is escalated to them.

### Conclusion

In this project, I demonstrated how the Zeek tool can be used to break packet captures into smaller log files and how analysts can narrow their attention to specific areas of focus. I also showed how tools such as Wireshark and VirusTotal can be used to verify any suspicious findings during initial analysis. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
