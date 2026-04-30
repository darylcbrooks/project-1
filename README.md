### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/z8XWL0fRxhM/0.jpg)](https://youtu.be/z8XWL0fRxhM)

### [ Project 35 - Network Security Analysis w/ Zeek](https://youtu.be/z8XWL0fRxhM)

The objective of this project is to build on the knowledge I previously shared regarding Zeek’s logging capabilities and demonstrate how these logs can be used to discover anomalous patterns in network traffic.

## Analyzing Web Traffic in Zeek Logs

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2001.png)

First, I switch to my Desktop directory (“cd Desktop/”), since this is where the packet capture file I will be using is stored. Next, I run the following command to extract data and generate the log files from the PCAP file needed for my analysis: “zeek -r windows-traffic-captured.pcap”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2002.png)

To view the log files that were extracted from the “windows-traffic-captured.pcap” file and stored in my local directory, I run the “ls -al” command. We can see that quite a few log files were generated, and I’m going to start by viewing the contents of “http.log”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2003.png)

Using the “cat http.log” command allows me to do so. It contains detailed information about HTTP traffic, including timestamps, client and server IP addresses, HTTP methods, request URIs, and HTTP status codes. By observing these entries, I can see that destination address 172.66.40.71 is consistently returning 400 Bad Request errors, and the repeated attempts to visit this address could be indicative of something malicious.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2004.png)

Next, I run the “cat dns.log” command to view the contents of the DNS log. It contains detailed records of DNS queries and responses, including timestamps, source and destination IP addresses, query types, and response codes. It appears that most of the requests are made from the IP address 192.168.1.10, as it appears most frequently as the source IP.

## Parsing Log Data to Narrow My Scope

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2005.png)

I want to filter the dns.log file to show only the entries where the source IP address is 192.168.1.10 and save the output to a new log file named “analyzed.log”. I execute the following command to accomplish this:
“cat dns.log | zeek-cut id.orig_h query qtype | grep “192.168.1.10” > analyzed.log”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2006.png)

Using the “cat analyzed.log” command to display the output of the new log file, I can now see a chronological history of the domains visited by 192.168.1.10.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2007.png)

Next, I want to sort this information in a way that will allow me to review the frequency of DNS requests to each domain listed in the “analyzed.log” file. To do this, I execute the “cat analyzed.log | sort | uniq -c” command. All of the domains visited seem like typical network behavior except for www.domains4bitcoins.com. My next steps would be to research this URL on a threat intelligence platform such as VirusTotal to determine if it’s malicious. In addition, isolating the 192.168.1.10 device from the network and checking for indicators of a cryptojacking attack (high resource usage, performance lags, other network errors, etc.) would also be a recommended action to take.

## Analyzing Protocol Activity

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2008.png)

I can also run the “cat” command to display the contents of the “conn.log” file to view a detailed summary of all network connections observed by the system (“cat conn.log”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2009.png)

Capturing a comprehensive record of connection attempts is crucial for network security analysis. As we can see from the output, a large number of connection records are contained in this log file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2010.png)

I can narrow this list by filtering for specific types of connections. The following command allows me to analyze SSL/TLS connections only and greatly reduce the amount of information I’m viewing at a given time:
“zgrep “ssl” conn.log | awk ‘{print $6}’ | sort | uniq -c | sort -nr”.
This analysis is crucial, as TCP, used primarily for SSL/TLS in secure communications, is vital for identifying normal versus suspicious traffic patterns. This command filters the log entries for SSL/TLS connections, extracts the destination port used in these connections, and then sorts and counts each occurrence to find the most frequently used ports. We can see that the returned output indicates that port 443 was the most frequently used destination port for SSL/TLS connections.

![Image](https://github.com/darylcbrooks/project-1/blob/project-35/Project%2035%20-%20Step%2011.png)

Last, I use the “cat ssl.log” command to view the contents of the SSL log. It contains detailed information about SSL/TLS sessions, including timestamps, client and server IP addresses, SSL version, cipher suite, certificate information, and session state. I can see that TLSv1.2 was the most frequently used protocol version for the SSL/TLS connections.

## Conclusion

In this project, I demonstrated how Zeek can provide the necessary data for cybersecurity analysts to detect abnormal network traffic patterns through log analysis. Having the ability to narrow the view to specific details of network traffic enables specialists to quickly find the root cause of these anomalies. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
