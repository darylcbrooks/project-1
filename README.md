### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/3nouIZvV2MY/maxresdefault.jpg)](https://youtu.be/3nouIZvV2MY)

### [ Project 38 - Understanding Path Interception and Its Risks in Code Execution ](https://youtu.be/3nouIZvV2MY)

In this project, I demonstrate how a path‑interception vulnerability can be exploited. Path interception occurs when code relies on environment‑based path resolution, which can allow an attacker to influence which system commands are executed. Here’s how the issue works:

## Reviewing the Vulnerable Script

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2001.png)

I begin by navigating to the “git” folder in my Desktop directory (cd Desktop/git). After confirming that the byteshred.c script is present (ls), I display its source code using cat byteshred.c.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2002.png)

In lines 47–53, the script creates a child process to run either dd or rm to overwrite and delete a file. Both commands are executed through the execlp() function. This function searches for the specified program by consulting the user’s $PATH environment variable. Because the search order can be influenced by users with write access to certain directories, an attacker may be able to introduce a malicious executable that is executed instead of the intended system binary.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2003.png)

For example, if the $PATH is /bin/:/myprogs/:/usr/bin:/usr/local/bin, attempting to run rm will cause the system to search each path component in that order. If a user has write access to /myprogs, they could place a malicious file named rm there, and it would execute before the legitimate /usr/bin/rm.

##  Exploiting the Vulnerability to Gain Elevated Privileges

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2004.png)

To demonstrate this behavior, I temporarily modify the $PATH variable so the current directory is searched first (export PATH=.:$PATH). After returning to the Desktop directory (cd ..), I create a simple C program named rm.c intended to replace the legitimate rm during execution (nano rm.c):

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2005.png)

I enter the following code into the empty C script file:

int main() {
    system("/bin/sh");
}

This code will spawn a root shell upon execution. Line 1 declares the main function, while line 2 is what actually spawns the shell, and line 3 closes the function. To save the changes, I press Ctrl+X, hit Y, and then press Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2006.png)

To compile my code, I use the “gcc rm.c -o rm” command.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2007.png)

Next, I create a test file (touch test_file) and run the vulnerable script against it (./byteshred test_file).

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2008.png)

Because the modified $PATH causes the script to call my local rm binary, I now have a shell with root level privileges.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2009.png)

I run the “id” command to verify my elevated privileges, and it returns the user and group privileges that are synonymous with root level access. This demonstrates how a manipulated $PATH can redirect execlp() to run my fake “rm” script in the current directory instead of the real “/bin/rm”.

## Confirming the Vulnerability Without Creating a Malicious rm

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2010.png)

I use the “exit” command to terminate my root session and remove the malicious “rm” executable that I created (“/usr/bin/rm ./rm). I use the touch command again to create another dummy file named “test_file” (“touch test_file”). To analyze the script’s behavior safely, I use strace to observe missing‑file lookups (strace ./byteshred test_file).

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2011.png)

This produces far too much output to know what’s going on.

![Image](https://github.com/darylcbrooks/project-1/blob/project-38/Project%2038%20-%20Step%2012.png)

To reduce the amount of output returned and to follow any child process produced upon the execution of “byteshred.c”, I run the following command: “strace -f ./byteshred test_file 2>&1 | grep ENOENT”. This approach reveals whether the script is attempting to execute commands that may not exist in the filesystem. The ENOENT (“No such file or directory”) results help confirm that the program is susceptible to path interception without requiring creation of a malicious replacement binary.

## Conclusion

Path‑interception vulnerabilities can be mitigated by using absolute paths for command execution, properly quoting path variables in scripts, and ensuring that file‑system permissions prevent unauthorized writes to directories included in $PATH. Regular system audits for unquoted or unsafe paths, along with application‑control solutions such as AppLocker, can further reduce exposure to this type of issue. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
