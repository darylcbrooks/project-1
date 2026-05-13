### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/J91hKeeHM2I/maxresdefault.jpg)](https://youtu.be/J91hKeeHM2I)

### [Project 43 - Mastering Access Control Lists for Enhanced Security 🔒  ](https://youtu.be/J91hKeeHM2I)

The basis of this project is to provide a demonstration of how access control lists can be created, maintained, and removed in a Linux-based environment. Access control lists are rules that specify which users or system processes can access a resource and what actions they can perform.

## Adding An ACL for New Users and Groups

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2001.png)

To create the first file that I will be working with throughout this project, I use the “touch” command to make a file called “file1” (“touch file1”). To view the access control list information for “file1”, I use the “getfacl file1” command. This lists the file’s properties, such as the file name, owner, group memberships, and permissions.

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2002.png)

To view the ACL information for “file1” while omitting the header data (file name, owner, and group membership), I run the “getfacl -c file1” command. The returned output now only displays the file’s permission set.

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2003.png)

Next, I want to create a new group to add to the access control list. To do so, I execute the “sudo groupadd users1” command, which creates a new group called “users1”. Then, I create a new user to add to the “users1” group. The “sudo useradd -g users1 user1” command adds a new user named “user1” to the group. I want to update the ACL to give both user1 and file1 read and write privileges, so I run the “setfacl -m u:user1:rw,m:r file1” command. The “mask” portion of the output shows the current ACL controls placed on file1, and the “setfacl” command is what enabled them. Finally, I verify these changes to the access control list and file permissions by running the “getfacl file1” command again.

## Setting ACLs with Octal Notation

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2004.png)

Updating user permissions in an access control list can also be done using octal notation. With the “setfacl -m u:user1:7 file1” command, the “user1:7” portion assigns execute permissions to the user “user1” when interacting with “file1”. The “getfacl file1” command is used again to verify the change in user permissions.

## Deleting ACL Entries for a User and ACL Controls for a File

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2005.png)

I can easily remove the file ACL entry for user1’s execution permissions by running the following command: “setfacl -x u:user1 file1”. The “-x” option specifies which permission to remove for the user. The output of “getfacl file1” verifies the removal of user1’s execution permissions for file1.

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2006.png)

To remove all access control list entries for the file, I run the “setfacl -b file1” command and verify the removal using the “getfacl file1” command. In the image above, we can see that the “mask” no longer appears in the output, indicating the removal of ACL controls for file1.

## Managing ACLs for Directories and Users

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2007.png)

In this next phase of the project, I create a new user account named “user2” (“sudo useradd user2”). Next, I use the “mkdir” command to create a new directory called “files”. To view the current permission sets for the new “files” directory, I execute the “getfacl files” command. Based on the output, we can see that users have read, write, and execute permissions in the directory, while everyone else has only read and execute permissions.

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2008.png)

To set the directory ACL to give “user1” and “user2” read and write permissions in the “files” directory, I run the following command: “setfacl -m d:u:user1:6,d:u:user2:6 files”. Similar to setting user permissions in file1, the “user1:6,d:u:user2:6 files” portion of the command assigns both user accounts read and write access in the directory ACL.

## Testing ACL Inheritance and Cleanup

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2009.png)

In this final phase of the project, I combine commands to switch to the “files” directory and create a new subdirectory called “files1” (“cd files; mkdir files1”). Once in the “files” directory, I use the “ls” command to verify the existence of the “files1” subdirectory. I then create a new file called “files2” with the “touch” command (“touch files2”). I run the “ls” command again to verify that the newly created file is present in the “files” directory. To view the current permission set for “files2”, I use the “getfacl files2” command. In the image above, when comparing the user permissions to the mask permissions, we can see that files2 has inherited the permissions of the “files” directory.

![Image](https://github.com/darylcbrooks/project-1/blob/project-43/Project%2043%20-%20Step%2010.png)

Similar to what I did with “file1”, to delete all default ACLs from the “files1” subdirectory, I execute the “setfacl -k files1” command. The “-k” option removes the default access control lists from the subdirectory. I use the “getfacl files1” command to verify that all the default ACLs were removed from the “files1” subdirectory. In the output, we can see that the mask value for the “files1” subdirectory (rwx) no longer matches the mask value of the “files” directory (rw-).

## Conclusion

The purpose of this project was to demonstrate how user access can be manually managed from the Linux CLI using access control lists. In larger enterprises, more automated or software-based alternatives are often used for efficiency. However, this is still great knowledge to have when automated solutions aren’t available and/or when managing smaller systems. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
