### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/PbDB67H4e6s/maxresdefault.jpg)](https://youtu.be/PbDB67H4e6s)

### [Project 41 - File Permissions and the Principle of Least Privilege in System Administration](https://youtu.be/PbDB67H4e6s)

In this project, I’ll be demonstrating several aspects of working with file and directory permissions in a Linux environment. Access permissions are vital to enforcing the principle of least privilege, ensuring that the correct users and groups have the proper levels of access to system resources.

## Viewing File Permissions

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2001.png)

To begin, I create a file called “file1” using the touch command (touch file1). I confirm that the newly created file has been added to my current working directory by using the ls command. To check the default permissions of "file1," I run the ll file1 command.

The first character in -rw-r--r-- denotes the file type, most commonly a directory (d) or a normal file (-), which is what this is. The next three characters denote what the owner of the file can do. The following three characters denote what group members can do with the file. The final set of three characters denotes what all other users can do with the file.

## Assigning Owner Execute Permissions

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2002.png)

To give the owner of “file1” execute permissions, I run the following command: chmod u+x file1 -v. In the image above, we can see that the octal notation for the file permissions has changed from 644 to 744 and that there is now an “x” after “rw”. Octal notation uses three digits ranging from 0 to 7 to denote file permissions. These values indicate that execute permissions have been added for the file owner.

## Assigning Read, Write, and Execute Permissions to All Users

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2003.png)

If I want to assign read, write, and execute permissions for all users who interact with “file1,” I use the following command: chmod a=rwx file1 -v. In addition to the displayed change in octal notation and updated permission fields, I verify the change by running ls -l file1. This command serves the same function as ll file1, showing the current set of permissions for the file.

## Adding Read-Only Permissions for All Users on a New File

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2004.png)

For the next phase of the project, I work with a different file. As before, I use the touch command to create a file named "file2" (touch file2). To assign read‑only permissions for all users, I run the following command: chmod a=r file2 -v. We can see that the file owner for "file2" initially had both read and write permissions, but after executing the command, only read permissions remain for the owner and everyone else.

Running chmod 544 file2 -v modifies the file permissions further by adding group execute permissions. I verify this change with the ls -l file2 command.

## Adding Permissions Using Octal Notation

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2005.png)

If I know the exact octal value for a set of permissions I want to apply, I can specify it directly in the chmod command. In the example above, I use chmod 766 file2 -v to add write permissions for everyone (owner, group, and others).

## Removing Permissions Using Octal Notation

Just as octal notation can be used to add permissions to files, it can also be used to remove them.

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2006.png)

In the following example, I change the octal value from 766 to 764 within the chmod command, which removes write permissions from all other users who interact with "file2" (chmod 764 file2 -v).

## Assigning Full Permissions to All Users Using Octal Notation

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2007.png)

The octal value that grants all users read, write, and execute permissions is 777. Think of it as the full “jackpot” of permissions, which makes it easy to remember. Needless to say, storing sensitive data in files with this permissions value is an insecure practice. To assign full permissions to “file2,” I execute the chmod 777 file2 -v command. In the image above, we can see that the execute flag (x) is present for all permission categories.

## Viewing Umask Values

A umask is a three‑digit value that refers to the read, write, and execute permissions for the owner, group, and all other users. Its purpose is to set default permissions for new files and directories without affecting existing ones.

![Image](https://github.com/darylcbrooks/project-1/blob/project-41/Project%2041%20-%20Step%2008.png)

To view the umask value for my current working directory, I run the umask command, which returns a value of 0022. I can also view the symbolic notation of the umask by running umask -S. This output provides a more human‑readable representation of default directory and file permissions.

## Conclusion

These are just some basic ways that file and directory access can be managed in Linux environments. Enforcing a security policy that facilitates information sharing on a need‑to‑know basis is vital to managing the confidentiality, integrity, and availability of data of all sensitivity types. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
