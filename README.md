### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/EmUkUolA_xo/0.jpg)](https://youtu.be/EmUkUolA_xo)

### [Project 15 - Hunting Down File Inclusions](https://youtu.be/EmUkUolA_xo)

In this project, I focus on discovering and remediating file inclusion vulnerabilities in a PHP script. These occur when the source code of an application includes files in a way that allows a potential attacker to manipulate input to read or execute unauthorized files on the hosting server. There are two variations of file inclusion vulnerabilities: local (LFI) and remote (RFI). In an LFI-based attack, the web application is forced to include files already present on the server, often using directory traversal techniques (i.e., ../../etc/passwd). This can lead to sensitive information disclosure and/or remote code execution. Remote file inclusion attacks cause the web application to include a remote file (usually via HTTP or FTP), allowing for arbitrary code execution on the hosting server. Preventative actions to mitigate file inclusion attacks include proper input validation, which helps prevent the exposure of sensitive data and remote code execution.

I will be automating the process of discovering these vulnerabilities by utilizing a PHP static code analyzer to perform scans on PHP scripts.

### Scan For RFI Vulnerabilities Using A PHP Static Code Analyzer

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2001.png)

The first thing I need to do is switch from my unprivileged Linux account to the admin account, as parts of this demonstration require elevated user privileges. I execute the “su admin” command and provide my account password to make the switch.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2002.png)

Now that I have the required privileges, I run the following command, which uses a PHP static code analyzer against the “lfi.php” file:

“~/vendor/bin/phpcs --extensions=php,inc,lib,module,info --standard=~/vendor/pheromone/phpcs-security-audit/example_base_ruleset.xml ~/examples/lfi.php ”.

Static analysis is the review of inactive code to identify vulnerabilities or errors. This analysis tool uses an embedded ruleset to scan lfi.php for the presence of any file inclusion vulnerabilities and report its findings. In the above image, notice the two warnings we get: “possible Remote File Inclusion detected”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2003.png)

Anytime the process of discovering vulnerabilities in code can be automated, it should be. Automation saves a significant amount of time and is far more efficient than manual code review (though someone should always be prepared to investigate suspected false positives). As an example, I’m going to use my text editor to open the cm.php file to show why automation is important (nano -l /var/www/html/src/core/controllers/cm.php).

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2004.png)

Observe the bottom-center of the image above. There are 333 lines of code in this one PHP script. It could take several hours for an individual to manually locate file inclusion vulnerabilities, and even then, oversights are possible. Automating the discovery of these vulnerabilities with predefined rulesets reduces this time from hours to seconds.

### Creating A New File Inclusion Ruleset

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2005.png)

Now I want to create a ruleset of my own to detect the presence of file inclusion vulnerabilities. Instead of writing one from scratch, I create a copy (lfi_only.xml) of the one used by the PHP static code analyzer to modify:

“cp ~/vendor/pheromone/phpcs-security-audit/example_base_ruleset.xml ~/vendor/pheromone/phpcs-security-audit/lfi_only.xml”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2006.png)

Next, I execute the following command to edit the modified ruleset using my text editor:

“nano ~/vendor/pheromone/phpcs-security-audit/lfi_only.xml”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2007.png)

I delete all the rules above “<rule ref=”Security.BadFunctions.EasyRFI”/>” and everything after it up to “</ruleset>” (it was too much code to capture in a single screenshot). As the new naming convention implies, I’ve modified this script to only look for file inclusion attacks. Before this change, the script also looked for other unrelated vulnerabilities. Once done, I use Ctrl + X to exit, type “Y” to save, and hit Enter to return to the command line.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2008.png)

### Scanning An Entire Directory w/ The New File Inclusion Ruleset

Now I use the following command to execute the PHP static code analyzer again — this time with the modified ruleset (lfi_only) scanning an entire directory (/var/www/html/) for vulnerabilities:

“~/vendor/bin/phpcs — extensions=php,inc,lib,module,info — standard=~/vendor/pheromone/phpcs-security-audit/lfi_only.xml /var/www/html/”

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2009.png)

This time, the scanner outputs far too much information to capture! This is where manual analysis is needed. After scrolling through several lines of output, I find an entry that catches my attention. On line 17 of a script named “gTable.php”, the analyzer has detected the potential presence of a remote file inclusion vulnerability. The “$path on include” portion of the code is what it flags. Let’s take a look ourselves to determine if this is a legitimate finding or a false positive.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2010.png)

To open the gTable.php script, I execute: “cat /var/www/html/gila/src/core/classes/gTable.php”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2011.png)

Now that I’m view the source code of the script, I can see the line in question ($path = $content). An attacker can set $content in “$path = $content; to a directory like “../../../etc/passwd” to enumerate users in a LFI attack or to a directory housing a malicious file in a RFI attack. “$this -> table = include $path;” includes wherever $path points to. Having verified that this finding is a legitimate file inclusion vulnerability, I move on to remediate the issue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2012.png)

Using my text editor again, I open the gTable.php file for editing by providing its home directory (“nano /var/www/html/gila/src/core/classes/gTable.php”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2013.png)

Once in the script, I remove the line of code “$this -> table = include $path;” which should eliminate the vulnerability. I use the Ctrl + X key combination to exit, type “Y” to save, and hit enter to go back out to the command line.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2014.png)

Now I want to take the modified version of gTable.php from the “mitigation” directory and save over the previous version containing the vulnerability. I execute: “cp ~/mitigation/gTable-mit4.php /var/www/html/src/core/classes/gTable.php”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2015.png)

To verify that the vulnerability has been remediated, I run a custom Python script to test whether an LFI attack can be performed against any file on my system (~/exploits/auto-exploit.py). The script returns a message stating that the LFI attempt failed, meaning my remediation was successful!

![Image](https://github.com/darylcbrooks/project-1/blob/project-15/Project%2015%20-%20Step%2016.png)
