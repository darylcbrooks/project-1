### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/lwDwmmK09Gw/maxresdefault.jpg)](https://youtu.be/lwDwmmK09Gw)

### [Project 36 - Network Data Collection w/ Honeypots](https://youtu.be/lwDwmmK09Gw)

This project will focus on how to configure a honeypot and demonstrate its capability to lure malicious users and track their behaviors. A honeypot is a decoy system or strategic trap designed to detect, deflect, or study attackers by posing as an attractive, vulnerable target.

## Configuring PenTBox as a Decoy FTP Server

PenTBox is a versatile, Ruby-based security suite designed for network and system testing. It is most known for its fast, configurable honeypot feature, which lures attackers so that their techniques can be monitored. In the following steps, I will be configuring PenTBox to pose as a vulnerable FTP server.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2001.png)

I begin by changing to the “pentbox-1.8” directory where the PenTBox executable file is stored (“cd Desktop/Files/pentbox-1.8/”). Once there, I use the “ls” command to view the contents of the directory and see the “pentbox.rb” Ruby file needed to initiate the honeypot. I then use the following command to boot PenTBox: “sudo ruby ./pentbox.rb”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2002.png)

Once booted, the PenTBox configuration menu appears. I select “2” to view the options for network tools.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2003.png)

Next, I choose “3” to select the Honeypot tool and “1” for Fast Automatic Configuration. In the image above, we can see that the honeypot has been activated on HTTP port 80.

## Testing PenTBox’s Intrusion Detection Capability

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2004.png)

I open a second terminal and use the “wget 127.0.0.1” command to request the simulated port from my loopback address; the results will be saved to the “index.html” file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2005.png)

In the PenTBox terminal, we can now see that an intrusion has been detected!

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2006.png)

Returning to the second terminal, I run the “cat” command with the “index.html” file to view its contents (“cat index.html”). The output appears to be the markup language for an “access denied” webpage.

## Creating a Decoy Banner

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2007.png)

In the PenTBox terminal, I press Ctrl+C to terminate the honeypot and execute the “pentbox.rb” file again to restart PenTBox (“sudo ruby ./pentbox.rb”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2008.png)

Again, I select option “2” for Network Tools.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2009.png)

Then I select “3” to use the honeypot once more, and this time I select “2” to manually configure it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2010.png)

Since I want the honeypot to appear as a decoy FTP server, I enter 21 as the port to open. Next, I enter “Welcome to the FTP Service” as a false banner message for malicious actors to see during banner‑grabbing attempts. I enter “y” to save a log of intrusion attempts, hit “Enter” to keep the default log name, and enter “n” to deactivate the beeping sound effect.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2011.png)

Back in the second terminal, I use NetCat in an attempt to grab the false banner that I just created in PenTBox. NetCat (aka the TCP/IP Swiss Army Knife) is a versatile command-line utility used for reading and writing data across network connections using TCP or UDP protocols. It serves as a flexible tool for network debugging, port scanning, data transfer, and acting as a listener for incoming connections. I use the following command to connect to port 21 of the honeypot being hosted by my loopback address: “ncat 127.0.0.1 21”. NetCat successfully returns the “Welcome to the FTP Service” banner!

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2012.png)

We can also see that the intrusion attempt has been detected again in the PenTBox terminal.

## False Banner Hosting and Grabbing Using NetCat Listener

Let’s see how the same banner‑grabbing scenario can be executed using NetCat’s listening capability.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2013.png)

In the PenTBox terminal, I press Ctrl+C to terminate the honeypot and run the following command to put NetCat into listening mode on alternative HTTP port 8080 and display a “Hello” message to anyone who connects to it:
“echo ‘Hello’ | ncat -l 127.0.0.1 8080”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2014.png)

Back in the second terminal, I run the “nc localhost 8080” command (“localhost” being my machine name) to connect to the listener being hosted on port 8080 and receive the “Hello” message.

## Hosting and Connecting to a Web Server Using NetCat

Besides banner grabbing, one of the many uses of NetCat is its capability to host local directories over the public internet. This enables NetCat to host decoy systems as well.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2015.png)

In the first terminal, I execute the following command to host a basic HTTP server over my loopback address using NetCat and display a “< html > Hello < /html >” message to anyone that connects to it:
“echo < html > Hello < /html > | nc -l -p 8080 127.0.0.1”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2016.png)

In the second terminal, I use the command “nc localhost 8080 | tee /tmp/blackhole” to connect to the web server that I’m hosting in the first terminal. This successfully returns the “< html > Hello < /html >” message.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2017.png)

Returning to the first terminal, I press Ctrl+C to terminate the NetCat web server. This time I’m going to host the web server again but execute code that returns a message only when a connecting user enters a specific query. The following command will output the message “valid response” when a connecting user enters “valid command”:
“ncat -k -l 127.0.0.1 8080 -c ‘read message; [ x”$message” = x”valid command” ] && echo “valid response”’.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2018.png)

In the second terminal, I enter “nc localhost 8080” to connect to the web server again. This time, I enter “valid command” once connected, which successfully returns the “valid response” message. This can be used to imitate the authentication process in order to fool an intruder into believing they have entered valid credentials into a real system.

## Using Scapy to Sniff Packets Generated by a Honeypot User

Scapy is a powerful, Python‑based interactive packet manipulation library and tool used to forge, sniff, dissect, and send network packets. I’m going to demonstrate how it can be used to view and analyze the network traffic generated by malicious users that enter a honeypot system.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2019.png)

Back in the first terminal, I press Ctrl+C to terminate NetCat and start Python (“sudo python3”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2020.png)

Next, I type “from scapy.all import *” to import the Scapy utility.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2021.png)

To initiate the packet sniffer on the loopback interface, I enter:
“sniff(iface=’lo’, prn=lambda x: x.summary())”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2022.png)

In the second terminal, I use NetCat again to connect to port 80 on my machine (“nc localhost 80”). This is done to test the packet sniffer by generating traffic. The connection is refused because there is currently no service listening on port 80. Back in the first terminal, however, we can observe that two SYN packets were sent to Scapy and it sent two RST packets back due to port 80 being closed. This can be problematic because a well‑versed adversary would know that their connection attempt failed upon receiving the RST packets.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2023.png)

Still in the first terminal, I press Ctrl+C and enter the “quit” command to exit Python.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2024.png)

To prevent connecting devices from receiving RST packets, I need to make adjustments to my local IPTable rules. First, I set the following rule to drop inbound RST packets so the connection is torn down before completion:
“sudo iptables -A INPUT -i lo0 -p tcp — tcp-flags RST RST -j DROP”.
Next, I set the following rule to drop outbound RST packets so they are never received by those connecting to the honeypot:
“sudo iptables -A OUTPUT -o lo0 -p tcp — tcp-flags RST RST -j DROP”.
I verify the application of these new IPTable rules by running the “sudo iptables -L” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2025.png)

Next, I will be setting up a custom FTP server and need to switch to the directory where the configuration file is stored (“cd /Desktop/Files/python_scripts”). Running the “ls” command displays all of the Python scripts located in the local directory, including the “server.py” file that I will be using. I execute the “cat” command with the file name to display its contents (“cat server.py”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2026.png)

Line 3 of the configuration file defines a lookup table of ports to use and their corresponding commands.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2027.png)

Lines 7–8: Checks that the destination port of a packet is one of these ports

Lines 10–14: Ensures that a sniffed packet is unique (necessary due to duplicate detections on loopback adapter)

Lines 19–25: Checks if a sniffed packet is a SYN packet and builds an appropriate SYN/ACK response

Line 26: Sends the SYN/ACK packet on the loopback adapter, listens for a single packet in response (the ACK in the TCP handshake)

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2028.png)

Lines 27–31: Builds and sends a packet containing the desired banner message based upon the received ACK packet (does not wait for a response)

Line 33: Listens on the loopback adapter for up to 100 packets. If a packet is a TCP packet, sends it to the reply function.

This server is designed to complete the TCP handshake and send a port‑appropriate banner message for FTP.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2029.png)

Next, let’s take a look at the “client.py” file (“cat client.py”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2030.png)

The structure of the client is nearly identical to that of the server, but the main difference is on lines 25–27, where it sends the initial SYN packet and an ACK packet to complete the TCP handshake.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2031.png)

To begin monitoring network traffic on my local loopback interface, I use “sudo tshark -i lo” to initiate TShark.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2032.png)

In the second terminal, I switch to the directory where “server.py” is stored (“cd /Desktop/Files/python_scripts”) and execute it with the “python3” command (“sudo python3 server.py”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2033.png)

I open a third terminal and switch to the same “python_scripts” directory. I execute the “sudo python3 client.py” command to begin sending SYN packets to my FTP server running in the second terminal.

![Image](https://github.com/darylcbrooks/project-1/blob/project-36/Project%2036%20-%20Step%2034.png)

Back in the first terminal where TShark is running, we can see the responses in TShark. This confirms that Scapy is operational!

## Conclusion

In this project, I demonstrated different approaches to running a decoy system and how to configure them to trick potential adversaries into revealing their TTPs (tactics, techniques, and procedures). I also demonstrated how a packet‑capturing utility such as Scapy can be used to track and capture adversarial network behavior on a honeypot system. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
