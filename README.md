Writing A DDoS Detection Script

I'm going to create a Python script to automate the identification of suspicious IP addresses.

1. nano ddos_automation.py (to create a python script named ddos_automation.py)

2. Added the following lines of code to the file:

import time
import subprocess
import re

log_file_path = "/var/log/suricata/fast.log"
detected_ips = set()

def block_ip(ip_address):
    subprocess.run(["sudo", "iptables", "-A", "INPUT", "-s", ip_address, "-j", "DROP"])
    print(f"Blocked IP: {ip_address}")

def monitor_logs():
    with open(log_file_path, "r") as log_file:
        log_file.seek(0, 2)
        while True:
            line = log_file.readline()
            if not line:
                time.sleep(1)
                continue

            if "ET DOS Possible DDoS Attack" in line:
                ip_match = re.search(r"(\d+\.\d+\.\d+\.\d+)", line)
                if ip_match:
                    attacker_ip = ip_match.group(1)
                    if attacker_ip not in detected_ips:
                        detected_ips.add(attacker_ip)
                        block_ip(attacker_ip)

if __name__ == "__main__":
    print("Monitoring for DDoS attacks...")
    monitor_logs()

^ This script monitors fast.log, detects IPs associated with DDoS activity, and automatically blocks those IPs. The block_ip function in the script uses iptables to drop incoming packets from the detected IP addresses, effectively blocking them.

3. Ctrl + X and Y (to save and exit)

Installing and Configuring Fail2ban for DDoS Blocking

Fail2ban - a free, open-source cybersecurity tool that protects Linux servers from brute-force attacks and unauthorized access. It works by continuously scanning system log files for repeated failed login attempts, then automatically updating the server's firewall to temporarily block the offending IP addresses.

4. sudo apt install fail2ban -y (to install Fail2ban) - passw0rd!

5. sudo nano /etc/fail2ban/filter.d/ddos.conf (to create a custom filter file to set up Fail2ban to recognize DDoS patterns in Suricata logs)

6. Added the following configuration item:

[Definition]
failregex = .*ET DOS Possible DDoS Attack.*src_ip=(?P<HOST>\d+\.\d+\.\d+\.\d+).*

7. Ctrl + X and Y (to save and exit)

Configuring A Fail2ban jail

A Fail2ban jail - specifies the conditions and actions to take when an IP address is detected as malicious.

8. sudo nano /etc/fail2ban/jail.local (to open the jail configuration with nano text editor)

9. Added the following configuration to the DDoS jail:

[ddos]
enabled = true
filter = ddos
action = iptables[name=DDOS, port=all, protocol=all]
logpath = /var/log/suricata/fast.log
bantime = 3600
findtime = 600
maxretry = 10

^ This configuration enables the DDoS jail and sets Fail2ban to monitor the Suricata log file fast.log for patterns matching DDoS attacks. If an IP address appears 10 times in a 10-minute window, Fail2ban will block it for one hour.

10. Ctrl + X and Y

11. sudo systemctl start fail2ban (to start Fail2ban)

12. sudo systemctl enable fail2ban (to start Fail2ban when system boots up)


I built a fully automated DDoS detection and mitigation system by integrating Suricata, a custom Python script, and Fail2ban. 
I began by installing and configuring Suricata to monitor network traffic and generate alerts for potential DDoS activity. 
Then, I developed a Python script to parse Suricata logs in real time and block malicious IP addresses using iptables. 
I, you configured Fail2ban with a custom filter and jail to automatically ban IPs that repeatedly trigger DDoS alerts
