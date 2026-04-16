### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/Ph6UqRAC7lU/0.jpg)](https://youtu.be/Ph6UqRAC7lU)

### [Project 29 - Enumeration with NMAP's Scripting Engine!](https://youtu.be/Ph6UqRAC7lU)


Throughout this project, I’ll be demonstrating a variety of advanced host enumeration methods using Nmap. This includes utilizing Nmap’s scripting engine, firewall evasion techniques, and timing profiles.

## Advanced Nmap Scans

In the first phase of the project, I’ll be using various scripts from the Nmap scripting engine to gather details about a target site (scanme.nmap.org). The Nmap scripting engine (NSE) is a powerful framework that extends Nmap’s core functionality, allowing users to automate a wide variety of networking tasks.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2001.png)

During this initial scan, I’ll be using the -sC switch to enable Nmap to run a scan using its default script (sudo nmap -sC scanme.nmap.org). The default script is designed to provide additional information about the target host. From the returned results, we can see that TCP ports 22 (SSH), 80 (HTTP), 8008 (alternative HTTP port), 8010 (XMPP), 9929 (nping-echo), and 31337 (Elite) are all open, while port 25 (SMTP) is being filtered. Additional protocol information displayed includes the SSH host key and the headers of the HTTP ports.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2002.png)

If I want Nmap to perform a scan to detect vulnerabilities in the target host, I would use the “ — — script=vuln” script (sudo nmap — — script=vuln scanme.nmap.org). In the above example, we can see that the script searches for several protocol‑specific vulnerabilities and appears to return a few Cross‑Site Request Forgery vulnerabilities that it found during its scan of the HTTP port.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2003.png)

Scrolling through the results a bit further, we can also see that the scan found an internal directory that should not be exposed to the public internet (apache/2.4.7) and the high likelihood of the target host being vulnerable to a denial‑of‑service‑related CVE (CVE‑2007‑6750: Slowloris).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2004.png)

The following Nmap script will allow me to collect detailed information about the target host’s SMB configuration: “SMB/OS discovery: sudo nmap — — script smb-os-discovery -p 445 scanme.nmap.org”. Even though the script is designed for SMB enumeration, I’m still required to specify the SMB port in use (in this case it’s the default 445 port) due to the SMB protocol utilizing multiple ports (137, 138, and 139). The Server Message Block (SMB) protocol is commonly associated with network shares and printers. The results for my scan notify me that SMB port 445 is closed on the target but interestingly provide an IPv6 address for it as well.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2005.png)

If I also wanted detailed information about the target host’s operating system in addition to the SMB configuration, I would just add the “-O” switch to the command line (“sudo nmap -O — — script smb-os-discovery -p 445 scanme.nmap.org”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2006.png)

The Nmap operating system scan “guesstimates” the target host’s OS. It returns a percentage or confidence score of the likelihood that a particular OS distribution is running. This is often due to some sort of security control in use by the target, which makes gathering more precise information more challenging for Nmap.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2007.png)

Another Nmap script that I can run is one that enables me to efficiently gather information about FTP services on the target host and identify any potential related vulnerabilities (sudo nmap -sV -p 21 — — script ftp-anon scanme.nmap.org). Fortunately for our target in this case, they’ve adhered to the highly recommended guidance of closing the FTP port in favor of more secure transfer protocols such as SSH.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2008.png)

The last Nmap script that I’ll run is one that will allow me to brute‑force the target’s DNS records. This should uncover additional hosts to target on its network. The results return two IPv4 addresses and two IPv6 addresses. The first pair of IPv4 and IPv6 addresses seem to be connected to the same device. This is where an attacker would begin enumeration on the newly found target and look for means of entry.

## Firewall and IDS Evasion Techniques

This next phase of the project will cover different stealthy scanning techniques that can be utilized with Nmap to circumvent network traffic monitoring controls such as firewalls and intrusion detection systems.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2009.png)

The first method that I demonstrate is using Nmap in decoy mode. The following command sends packets from both real and fake IP addresses to mislead the IDS or firewall about the scan’s origin: “sudo nmap -D RND:10 — — vv -T4 scanme.nmap.org”. In this example, my scan is using 10 random decoys to hide the source IP, returning verbose output, and using an aggressive timing profile.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2010.png)

In the above results, we can see the returned output is a bit more detailed than some of the previous scans on the same target host. It now includes a reason for port statuses as well as time‑to‑live (TTL) packet hops.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2011.png)

The next stealth Nmap scanning technique that I perform is including the randomized hosts option in my command (“sudo nmap -iR 4 scanme.nmap.org”). In this instance, I scan four random targets, which makes it harder for an IDS/IPS to detect due to the unpredictable scanning order. However, the drawback to this scanning strategy is that it renders inconsistent results to the user. Many times, Nmap may not return any output due to the status of the random targets it scans. Fortunately, I was able to successfully enumerate a host using the randomized scanning technique (after several tries 😏).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2012.png)

Nmap can be used to fragment a single packet into smaller pieces, which will make the scan harder to detect by firewalls and IDS systems (“sudo nmap -f scanme.nmap.org”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2013.png)

Lastly, a scanning strategy known as Source Port Manipulation can be applied. This is when the actual port numbers are changed to standard port numbers and combines OS detection, script scanning, and traceroute together. In the above example, we can see that this type of scan is combined with a pingless (-Pn), SYN (-sS), and aggressive (-T4) scan (“sudo nmap -A -T4 -Pn -g 80 -sS scanme.nmap.org”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2014.png)

The drawback to running this sort of scan is that it takes a very long time to complete and returns an exhaustive amount of output. Skipping sending ICMP packets to the target (-Pn) and applying a faster timing profile (-T4) is supposed to speed the scan up, but even when these strategies are applied, the scan still takes a very long time to complete. If used, I highly recommend saving the output of this type of scan for later analysis (-oN [filename]).

## Scanning Using Timing Strategies

These next sets of Nmap scanning strategies revolve around how to use the timing and speed of the scan to evade detection and decrease the amount of time spent on host enumeration.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2015.png)

I’ve covered the strategic use of Nmap timing profiles before (https://github.com/darylcbrooks/project-1/blob/project-3/README.md) , but I’ll review it again since it is highly relevant to the subject matter. Users can control how fast Nmap scans hosts for open ports. The faster Nmap runs, the easier it is for it to be detected. Also, running Nmap at its fastest speed can potentially crash the target system, so we must be careful with how we use these speed parameters. The “T” parameter is what enables us to control the speed of the scans. The speed parameters that we can use on the command line are as follows (from slowest to fastest):

* T0 (paranoid)
* T1 (sneaky)
* T2 (polite)
* T3 (default)
* T4 (Aggressive)
* T5 (Insane)

The lower the number, the slower the scan, but the stealthier we are and the lower the risk of being detected. The opposite is true with the higher‑numbered parameters, which also increase the risk of a system crash. In the above example, I run an aggressive Nmap scan (-T4) against scanme.nmap.org (“sudo nmap -T4 scanme.nmap.org”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2016.png)

The next scan type involves setting the maximum number of scan retries to a finite value. This ensures that Nmap runs as short or as long as desired against targets. In this example, I set Nmap to scan a target three times before returning results (“nmap — max-retries 3 scanme.nmap.org”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2017.png)

I can also set the maximum amount of time Nmap can conduct a scan before timing out. In the above image, we can see that I chose to conduct a longer Nmap scan by setting the maximum amount of time to 30 minutes (“nmap — host-timeout 30m scanme.nmap.org”). The total amount of time set doesn’t have to be fully utilized, but it sets a limit on how long the scan can run.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2018.png)

An alternative means of adjusting Nmap’s scanning intensity is by using the “ — — version-intensity” script. The same numerical values used in the timing profiles are still applicable for this Nmap script. In the above example, I set the scan intensity to high (5) and saved the results in normal format to a text file (“nmap -sV — — version-intensity 5 scanme.nmap.org -oN detailed_scan.txt”). The name of the file that the output is saved in is “detailed_scan.txt”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2019.png)

To verify that the output file is saved in my current working directory, I use the “ls” command. We can see that “detailed_scan.txt” is present.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2020.png)

I also want to verify the integrity of the output itself (the same results displayed in the initial Nmap scan are the same results contained in the output file). To do so, I use the “cat” command along with the file name to display its contents (“cat detailed_scan.txt”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2021.png)

The exact same output seen in the initial Nmap scan is displayed in “detailed_scan.txt”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2022.png)

Lastly, I want to run an Nmap scan that collects detailed information about the HTTP service being hosted on port 80 of the target host. I want this to include details such as the web page title and supported HTTP methods, and then I want the output to be saved to a text file as well. I use the following command to do so: “nmap — — script=http-title,http-methods scanme.nmap.org -p 80 -oN http_details.txt”. The name of the file that the output is saved in is “http_details.txt”, and as before, I want to verify the file’s presence in my current working directory and the accuracy of its contents.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2023.png)

As before, I run the “ls” command, and it returns the list of files present in my current working directory, including “http_details.txt”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-29/Project%2029%20-%20Step%2024.png)

Last, I execute the “cat” command along with the file name to verify http_details.txt’s contents (“cat http_details.txt”), and everything displayed matches the results of the initial Nmap scan.

## Conclusion

These are only a few (but effective) advanced Nmap scanning techniques that can be used to gain more detailed information, evade security controls, and directly control the amount of time spent enumerating targets. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
