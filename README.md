### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/l8Ok7Iok2VA/0.jpg)](https://youtu.be/l8Ok7Iok2VA)

### [Project 26 - Automating Patch Management with Ansible and Cockpit 🛠️](https://youtu.be/l8Ok7Iok2VA)

Patch management is the process of managing updates for software components and operating system kernels. The goal of this project is to demonstrate how patch management can be simplified in a Linux-based environment through the use of automated tools.

## Configuring Ansible for Automated Patch Management

Ansible is an open‑source automation tool used for IT tasks such as configuration management, application deployment, intraservice orchestration, and provisioning. I will be configuring Ansible to automatically search for and install updates for installed software packages, as well as remove any legacy versions that are no longer needed.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2001.png)

The first step is to generate an SSH key pair (public and private key) on the server. This will allow me to authenticate through SSH to the host without having to provide a password each time. I use the ssh-keygen command and press Enter three times to produce the key pair.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2002.png)

Next, I execute the following command to copy the public SSH key to the client to allow passwordless authentication:
ssh-copy-id ubuntu-user@192.168.1.100.
After that’s complete, I use the nano text editor to open the Ansible hosts file so I can add a few lines to specify the target host’s IP address (sudo nano /etc/ansible/hosts).

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2003.png)

Once inside, I scroll to the bottom of the file and add the following syntax, which tells Ansible which IP address to check for updates on:

[myclients]
client1 ansible_host=192.168.1.100 ansible_user=ubuntu-user

The ansible_host=192.168.1.100 portion defines the contents of the myclients group. From now on, anytime Ansible checks for updates on 192.168.1.100, it will simply refer to myclients. To save the changes, I press Ctrl+X, hit Y, and then press Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2004.png)

To test that Ansible can now communicate with the target host, I have it ping myclients using:

ansible myclients -m ping.

The first line of the response confirms a SUCCESS, and the last line returns a pong response to my initial ping.

### Creating an Ansible Playbook

My next step is to create the actual playbook containing the “play” (automated process) that Ansible will use to periodically update the target host. An Ansible playbook provides a scriptable, repeatable, and easy‑to‑understand way of defining infrastructure and application processes.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2005.png)

I use the nano text editor to create a playbook file named check-updates.yaml. As implied, the playbook file is written in a human‑readable data‑serialization language known as YAML, which is commonly used for configuration files, data exchange, and DevOps tools including Ansible and Kubernetes.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2006.png)

I add the following code:

---
- name: Update System Packages
  hosts: myclients
  become: yes
  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes
        cache_valid_time: 3600
    - name: Auto-remove unnecessary packages
      ansible.builtin.apt:
        autoremove: yes

The Ansible play first checks whether the installed software packages are up to date. If updates are available, Ansible installs them and automatically removes any unnecessary packages currently installed on the system. To save the changes, I press Ctrl+X, hit Y, and then press Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2007.png)

To test the functionality of the playbook, I manually execute it using:

ansible-playbook check-updates.yaml --ask-become-pass.

I can see Ansible updating system packages, checking versions, updating the apt cache, and removing any legacy packages.

## Adding a Server using Cockpit

Now that I’ve created an automated process to update the target host, I want to effortlessly add it to a production environment. This can be achieved through the Cockpit application. Cockpit is an open‑source, web‑based graphical interface for managing Linux servers.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2008.png)

To access Cockpit, I open my web browser and navigate to where it’s hosted:

http://192.168.1.101:9090.

I then enter my credentials at the login screen.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2009.png)

After authenticating, I click the Dashboard icon on the left‑most side of the window. The dashboard lists the existing servers in my production environment. So far, there appears to be only one, named ubuntu19. I click the blue plus icon in the bottom‑right corner to begin adding a new server.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2010.png)

In the window that appears, I enter the IP address of the target host (192.168.1.100) and click Add.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2011.png)

Now we can see two servers in the production environment — both named ubuntu19 — but 192.168.1.100 is easily identifiable because it’s highlighted in green. This is how I can differentiate the performance of each server from the resource usage tabs above. I click on the green ubuntu19 server to view more details about the device.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2012.png)

The resulting panes allow me to view system information, logs, and network interfaces associated with 192.168.1.100.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2013.png)

Now that I’ve added the server, I return to the command terminal and initiate an SSH connection using the key pair created at the beginning of the project. The successful connection verifies not only that the server is online in my production environment but also that the SSH key pair allows passwordless authentication.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2014.png)

I use the pwd command to verify my current working directory, then manually update the package list installed on the server (sudo apt-get update).

## Configuring Automatic Updates

I also want to demonstrate how automatic updates can be configured without using a tool such as Ansible.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2015.png)

First, I execute sudo apt list --upgradable to check which packages are available for upgrade before enabling automatic updates.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2016.png)

This returns a large list of software packages. Next, I use the nano text editor to open the apt configuration file to enable and configure automatic updates (sudo nano /etc/apt/apt.conf.d/20auto-upgrades).

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2017.png)

Inside the file, I can see the current update settings and their values.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2018.png)

I change the values of Update-Package-Lists (line 1), Download-Upgradable-Packages (line 2), and Unattended-Upgrade (line 4) to 1 to enable them. I then change the value of AutocleanInterval to 7 so the system automatically runs apt-get autoclean (which removes cached package files) every seven days. I save my changes by pressing Ctrl+X, Y, and Enter.

## Using WP-CLI

As a bonus, I’m going to demonstrate how the WordPress Command Line Interface can be used to manage automated updates to themes and plugins on a WordPress CMS. The WP‑CLI is a powerful tool that allows users to manage WordPress websites from the command line. Managing updates for themes and plugins is vitally important because they are common attack vectors. While the core CMS may be secure, an outdated or unsupported theme or plugin can still contain vulnerabilities that can be exploited if not properly patched.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2019.png)

In the terminal, I use the following command to display a full list of all installed themes on the hosted WordPress CMS:

wp theme list --path=/srv/www/wordpress.

From the returned table, we can see that updates are available for each installed theme, auto‑update is disabled for all of them, and only the Reykjavik theme is active.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2020.png)

Next, I execute:

wp plugin list --path=/srv/www/wordpress.

All but three plugins have updates available, all but two are active, and auto‑update is disabled for all of them.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2021.png)

Back in the web browser, I navigate to the WordPress admin panel (http://192.168.1.100/wp-admin) and enter my credentials.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2022.png)

After authenticating, I click the Appearance menu on the left and choose Themes.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2023.png)

Since only the Reykjavik theme is active, I hover over it and click Theme Details.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2024.png)

On the next page, I select Enable Auto‑Updates on the right side. This ensures the theme will receive automatic updates as they’re released by its developer.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2025.png)

Next, I click Plugins on the left to enable automatic updates for plugins as well.

![Image](https://github.com/darylcbrooks/project-1/blob/project-26/Project%2026%20-%20Step%2026.png)

The page lists all installed plugins. Fortunately, all active plugins appear in bold, which makes it easy to identify which ones I should enable auto‑updates for. I scroll through the list and click Enable auto‑updates for each active plugin.

## Conclusion

The purpose of this project was to demonstrate various ways and tools that can automate patch management. Any opportunity to automate simple but repetitive security‑maintenance tasks should be explored. Automation saves IT and security teams time, allowing them to focus on more dynamic security priorities. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
