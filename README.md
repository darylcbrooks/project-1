### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/1UlIBezp4Po/0.jpg)](https://youtu.be/1UlIBezp4Po)

### [Project 21 - Understanding and Configuring IP Tables in Linux ](https://youtu.be/1UlIBezp4Po)

My objective in this project is to demonstrate the functionality of IPtables by configuring several different ingress and egress rules used to manage traffic on a local network. IPtables is a command-line utility in Linux that configures the kernel’s built‑in netfilter firewall framework. It manages network traffic by filtering packets (allowing or blocking IP addresses or ports), performing Network Address Translation (NAT) for IP masquerading, and maintaining stateful inspection of connections.

## Building An Understanding Of IPTable Rules

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2001.png)

Before jumping into creating and adding new network traffic rulesets, I want to provide an example of some IPtables rules to give context to the actions I’ll take later. I execute the following command to run a shell script that loads IPtables from a text file named “iptable-rules.txt”:
sudo sh -c ‘iptables-restore -v < /iptable-rules.txt’.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2002.png)

Now that I’ve loaded the IPtable rules on my system, I use the “route” command to display the host’s routing table. It shows that I have an IP address of 192.168.1.0 and that my machine is running on the “ens32” network interface.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2003.png)

To view the current IPtables rules, I execute the “sudo iptables -S” command. The INPUT (INPUT ACCEPT) and FORWARD (FORWARD ACCEPT) chains currently allow all packet traffic, but the OUTPUT chain (OUTPUT DROP) blocks any packet initiated by the firewall host from being routed. This restricts user access to only trusted online resources, making traffic easier to control and threats easier to identify. The next rule (OUTPUT -m state — — state RELATED,ESTABLISHED -j ACCEPT) routes already established connections, and the last two rules allow the firewall to make DNS (OUTPUT -p udp -m udp — — dport 53 -j ACCEPT) and HTTPS (OUTPUT -p tcp -m tcp — — dport 443 -j ACCEPT) connections. IPtables rules are checked from top to bottom, first by matching the packet’s state and then inspecting headers for the specified destination ports. If the packet doesn’t match, it gets dropped. Chain policies are checked last.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2004.png)

Next, I use the “sudo iptables -L” command for a more detailed view of the rules within each chain. These resemble a more traditional firewall format.

## Adding An ICMP/Ping Rule

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2005.png)

Having an understanding of the current rules implemented in the IPtable, I want to begin adding a few new rules of my own. The first rule allows my machine to send ICMP packets to ping other devices. Remember, the current configuration only allows outgoing HTTPS and DNS packets and drops everything else. The following command adds the ping rule:
sudo iptables -v -A OUTPUT -p icmp -j ACCEPT.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2006.png)

I run three different tests to verify that ICMP traffic can leave my local network. First, I run an Nmap scan targeting ports 53 and 443 of another device on my network. Part of an Nmap scan involves sending ICMP packets to targets, and I want to ensure that adding this new rule hasn’t negated the previously set egress rules. I run:
nmap 192.168.1.100 -p53,443

The results confirm that I receive responses indicating the status of ports 53 and 443 on the host at 192.168.1.100.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2007.png)

Next, I test whether outgoing ICMP traffic in general is allowed. I run a command that sends a total of four ICMP ping packets to 192.168.1.100:
ping 192.168.1.100 -c 4

This results in four responses from the device, confirming that it is up and reachable on the network.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2008.png)

The last test verifies what the first test also measured: that the output rules for HTTPS and DNS are still active. I open a web browser and navigate to https://192.168.1.100:443. Specifying port 443 ensures that I’m reaching the service hosted on that specific port. I’m taken to a webpage that displays “It works!”, confirming that I can still reach a secure website.

## Adding A Traceroute Rule

Traceroute is a network diagnostic tool that maps the path data takes from its source to a destination IP address, identifying each router hop along the route. This is measured using packet TTL, or “time to live,” responses. I now want to add a rule that specifically allows outbound traceroute traffic.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2009.png)

First, I create a rule that allows routing of new connections:
sudo iptables -A OUTPUT -m state — — state NEW -j ACCEPT

Then I create the rule that allows traceroute itself:
sudo iptables -A OUTPUT -p udp — — dport 53 -j ACCEPT

Finally, I test whether traceroute works by sending traffic to 192.168.1.100:
traceroute 192.168.1.100

The second line of the results indicates that it only took one hop for the packet to reach its destination.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2010.png)

Running the “sudo iptables -L” command again, I can now see the new ICMP packet rule I created, followed by the rule for routing new connections, and finally the traceroute rule at the bottom of the list.

## HTTPS and DNS Replies Based On Outgoing Requests

The last set of rules I want to add are ones that allow only HTTPS and DNS responses to outside addresses.

![Image](https://github.com/darylcbrooks/project-1/blob/project-21/Project%2021%20-%20Step%2011.png)

To allow all incoming HTTPS responses related to outgoing requests initiated by the host, I execute:
sudo iptables -A INPUT -m state — — state RELATED,ESTABLISHED -j ACCEPT

Next, I create a rule that allows new connections since DNS requests initiate new sessions:
sudo iptables -A INPUT -m state — — state NEW -j ACCEPT

Finally, I create a rule that drops all communication to any other ports:
sudo iptables -P OUTPUT DROP

## Conclusion

This project served as a demonstration of how to safely allow outbound network traffic while preserving a hardened attack surface. If my security policy only allows members of my organization to visit and potentially conduct business with secure websites, then it makes sense to configure my IPtable or firewall rules accordingly — while also ensuring that internal IT staff maintain access to ping and traceroute for troubleshooting. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
