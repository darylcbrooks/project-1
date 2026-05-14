### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/6h_10tg36OE/maxresdefault.jpg)](https://youtu.be/6h_10tg36OE)

### [Project 44 - Understanding and Managing Special Permissions in Linux](https://youtu.be/6h_10tg36OE)

This project will cover various aspects of special file and group permissions. Special file and group permissions are advanced security controls in Linux operating systems that go beyond standard read, write, and execute permissions. They allow administrators to grant users temporary elevated privileges to run specific programs, ensure files inherit the correct group ownership, and protect shared directory spaces from unauthorized deletion.

## Setuid bit

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2001.png)

I begin by viewing the permissions for the “su” (switch user) command. This command is typically used to elevate a user’s session to root or to another user account. Above, we can see the resulting output is “-rwsr-xr-x”. The “s” in the user set of permissions stands for setuid. Setuid is a special permission that allows a user to run an executable file with the permissions of the file’s owner instead of their own. When a normal user executes this command, it will run as if root is running it; therefore, the user is able to run it successfully and get the desired result. The “su” command is owned by root. While setuid has advantages for legitimate system users, it can be problematic in the case of an intrusion or when enforcing the principle of least privilege.

The following command removes the setuid bit from the “su” executable: “sudo chmod u-s /usr/bin/su -v”. We can see the octal notation changes from 4755 to 0755, removing the special permission from “su”. This can also be verified by running the “ll /usr/bin/su” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2002.png)

If I want to add the special permission back to the “su” command, I use “sudo chmod 4755 /usr/bin/su -v”. Specifying the 4755 octal value accomplishes this. To verify that the setuid bit has been restored, I execute the “ll /usr/bin/su” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2004.png)

To view all files on my system that have a setuid bit, I use the following command: “sudo find /usr/bin -perm -4000”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2005.png)

In the image above, we can see the list of files containing the special user permission.

## Creating and Managing Users and Groups with Special Permissions

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2006.png)

I begin this next phase of the project by creating a new directory named “shared_dir1” (“sudo mkdir /shared_dir1 -v”). I then create a group called “shared_grp” with a group ID of 9999 (“sudo groupadd -g 9999 shared_grp”). Next, I create two new users, user100 and user200 (“sudo useradd user100”; “sudo useradd user200”). I add both new users to the “shared_grp” group (“sudo usermod -G shared_grp user100”; “sudo usermod -G shared_grp user200”). Finally, I change the ownership and owning group of the “shared_dir1” directory from “root” to “shared_grp” (“sudo chown root:shared_grp /shared_dir1”). Now it’s time to activate the setgid bit.

### Setting the Setgid

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2007.png)

Setgid is used with a shared directory so that any subdirectories or files automatically receive the directory’s owning group. I execute “sudo chmod g+s /shared_dir1 -v” to set the setgid bit on the “shared_dir1” directory. We can now see that the octal value has changed from 0755 to 2755, adding the special group permission to the directory. I can also verify this change by running “ll -d /shared_dir1”.

## Viewing and Setting Sticky Bits

Sticky bits protect files and subdirectories owned by regular users from being deleted or moved by other regular users.

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2008.png)

I use the “ll -d /tmp /var/tmp” command to display that the sticky bit is set for the “/tmp” and “/var/tmp” directories, represented by a “t” in the “other users” permission set. However, when running “ll -d /var”, I can see that the “/var” directory does not have a sticky bit set.

![Image](https://github.com/darylcbrooks/project-1/blob/project-44/Project%2044%20-%20Step%2009.png)

I set a sticky bit for the “/var” folder by specifying the octal value in the command: “sudo chmod 1755 /var -v”. To confirm that the change was applied, I run “ll -d /var”. Now we can see that the executable permission in the “other users” permission set has been changed to a sticky bit, represented by “t”. To remove the sticky bit and reset the permission sets for the “/var” folder, I use the following command: “sudo chmod o-t /var -v”. The output of this command and the “ll -d /var” command confirm the sticky bit’s removal.

## Conclusion

For functions such as “su”, special Linux permission sets are helpful when elevated privileges are needed to accomplish certain tasks. However, assigning them to and leaving them in place for other system attributes could present a threat to the confidentiality, integrity, and availability of the information being stored. Knowing how to remove them where and when necessary is key to maintaining a hardened attack surface. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉

