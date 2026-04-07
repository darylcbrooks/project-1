### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/wncpnuSTcYE/0.jpg)](https://youtu.be/wncpnuSTcYE)

### [Project 24 - Error Based and Union Select SQL Injections! 💉 ](https://youtu.be/wncpnuSTcYE)

Throughout the following project, I demonstrate the effects of both error‑based and UNION SELECT SQL injection attacks. SQL injection is a type of security vulnerability that occurs when an attacker is able to manipulate or inject SQL code into a query, typically through user input. Vulnerability to this sort of attack can be mitigated through input sanitization and character‑escaping techniques to prevent the processing of special characters that are often used in database queries or code execution.

## Error-based SQL Injection

Error‑based SQL injection occurs when an attacker deliberately forces a database to generate error messages. By analyzing these detailed, verbose errors, the attacker gains valuable information about the structure and contents of the backend database, such as table names, column names, and data types.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2001.png)

To demonstrate how this works, I use a mock website that is intentionally vulnerable to various SQL injection attacks. I open my web browser and navigate to http://192.168.100.101, where the website is hosted. The homepage contains a user registration form requesting basic information such as username, password, real name, etc. I populate the text boxes with dummy data and click the “Submit” button to continue.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2002.png)

I am then presented with a welcome page containing a search bar used to query information stored on the website. I enter the following SQL query into the search bar to test what type of error message will be returned: “‘order by 1 — — //”. “ORDER BY n — — //” is typically used to determine the number of columns in a SQL query’s SELECT statement when the exact structure of the query is unknown (n represents an integer). When I click the “Submit” button, the query disappears and nothing else happens. This tells me that the webpage is vulnerable to SQL injection and that the backend query contains at least two columns.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2003.png)

To determine the total number of columns present in the backend database, I continue entering the same query, incrementing the integer by one each time, until I receive an error message indicating that no such numbered column exists.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2004.png)

Once I reach “order by 6 — — //”, an error is finally returned, indicating there are five columns in the table behind the webpage. This is an example of the type of database enumeration and attribute deduction an attacker can perform if an input field is susceptible to SQL injection.

## Union Select SQL Injection

A UNION SELECT SQL injection is a variation of the attack that leverages the UNION SQL operator to combine the results of two or more SQL queries into a single result set. It allows an attacker to retrieve information from the database that they are not authorized to access.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2005.png)

Remaining on the same page that displayed the previous error message, I enter the following SQL query to test the webpage’s vulnerability to UNION SELECT injections: “‘ union select null, null, database(), user(), @@version — — //’”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2006.png)

Based on the column headers, it appears that the site is supposed to return product information. However, the returned output contains the database name (sqltraining), the user and hostname (user@localhost), and the database and version being used (MariaDB-1 10.11.8). This equips an attacker with an account to target, along with a specific database type and version that can be researched for known vulnerabilities.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2007.png)

Next, I want to test how much information I can extract from the database, so I enter the following SQL query: “‘ union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema = database() — — //’”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2008.png)

As requested, the site returns a list of tables (“products” and “users”), the types of information those tables store, and the database name.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2009.png)

Since there does not appear to be much limitation on what information can be retrieved, I query for more sensitive data: “‘ union select null, id, username, password, fname from users — — //’”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-24/Project%2024%20-%20Step%2010.png)

This returns usernames, password hashes, and potential real user identities (the last line reveals the real name I entered during registration). Exploiting this vulnerability saves an attacker the time required to perform a brute‑force dictionary attack to obtain valid credentials. At this point, all they would need to do is load the password hashes into tools like John the Ripper or Hashcat to crack them or locate a Metasploit module to “pass‑the‑hash” and authenticate to the backend web server.

## Conclusion

The goal of this project was to demonstrate the importance of input validation testing before launching a web application to the public internet, as well as the potential consequences of failing to do so. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
