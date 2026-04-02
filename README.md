### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/P0AnEEz0SYU/0.jpg)](https://youtu.be/P0AnEEz0SYU)

### [Project 22 - A Cure To The Common Code Vulnerability](https://youtu.be/P0AnEEz0SYU)

In this project, I’ll be diving into exploiting and mitigating some scripting‑related vulnerabilities, specifically using Python and XML machine languages. To begin, I’ll be using the Pickle module to serialize and deserialize Python objects. Serialization is the process of converting data objects or structures into streams of bytes for storage or transmission over a network. A common example of this is sending user data to a database. The reverse process, known as deserialization, involves reconstructing the byte stream back into objects or structures — for example, retrieving user data from a database and modifying it.

## Serialization w/ The Python Pickle Module

Pickling is the process of converting a Python object hierarchy into a byte stream. Examples include lists, dictionaries, or custom class instances. This allows objects to be saved to a file or transmitted over a network. Here’s how it works:

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2001.png)

First, I execute the “python3” command to start the Python 3 interpreter.

Next, I import the Pickle module using the “import pickle” command.

To create the dictionary file for this demonstration, I name it “test” and specify its contents (“{‘firstname’:’john’,’lastname’:’doe’}”).

Then, I create the output file (“outfile”) where the pickled data will be stored (“outfile = open(‘test_file’, ‘wb’)”).

To store the dictionary contained in “test” into “outfile,” I run “pickle.dump(test, outfile)”.

Finally, I close the output file using “outfile.close()”.

## Unpickling

Unpickling is the inverse operation — converting a byte stream back into a functional Python object hierarchy.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2002.png)

To open the file containing the stored data, I run: “pickle_file = open(‘test_file’, ‘rb’)”.

To deserialize the object, I execute: “new_test = pickle.load(pickle_file)”.

To view the deserialized object, I use “print(new_test)”. As shown in the image, the contents of the original dictionary file appear.

The “exit()” command takes me out of Python.

## Code Execution w/ Pickle

Now that I’ve demonstrated how object serialization and deserialization work using the Pickle module, I’ll explore whether any vulnerabilities can be exploited during execution.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2003.png)

I open my browser and navigate to the mock website at http://192.168.1.101:5000.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2004.png)

Before interacting with the site, I switch to the directory where the website’s source code is saved: “cd Documents/lab/python-Code/vulnerabilities/”.

Running “ls” lists the directory contents, where I see the source code file pickle_vuln.py.

I then use the nano text editor to view its contents (“nano pickle_vuln.py”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2005.png)

The script shows that the data is first decoded from Base64 and then unpickled using pickle.loads(). To exploit this implementation, the payload must first be pickled using pickle.dump() and then encoded to Base64.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2006.png)

I navigate back two directories using “cd ..” twice.

In the “lab” directory, I run “ls” to confirm that the exploitation script is present.

I see “exploit.py” and use “cat” to display its contents.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2007.png)

In this script, the default command is set to “whoami”, which executes the Linux whoami command and returns the user ID of the account running on the backend server.

The “COMMAND = sys.argv[1]” line allows different payloads to be pickled without modifying the script each time.

The reduce method in the pickle module specifies how objects should be serialized and returns a tuple describing how to reconstruct them during deserialization. The method returns (os.system, (COMMAND,)), where os.system is the callable and COMMAND is the argument passed to it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2008.png)

To generate the Base64‑encoded output of “whoami”, I run: “python3 exploit.py whoami”, and copy everything between the single quotes.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2009.png)

Returning to the mock website, I click the “Pickle” option, paste the Base64‑encoded output into the textbox, and click “Send”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2010.png)

The script works as expected — I am returned the user ID “ubuntu-user”!

Here are some Pickling best practices to remember:

* Never unpickle data from untrusted sources. Doing so risks arbitrary code execution with the privileges of the unpickling process.
* Transmit pickled data only over encrypted connections to prevent interception or modification.
* Use cryptographic signatures to sign pickled data so recipients can verify it hasn’t been tampered with.
* Consider using safer serialization formats such as JSON.

## XML Parsing

Now I’ll move on to exploiting and mitigating XML‑related vulnerabilities. Before jumping into attacks, I want to demonstrate how XML parsing works. XML is a human‑ and machine‑readable markup language used primarily to encode and structure documents.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2011.png)

To access the part of the mock website where XML parsing is demonstrated, I click “XML” on the far left. On the next page, I paste the following syntax into the textbox and click “Send”: “<test>testing</test>”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2012.png)

The output displays only the text between the XML tags — in this case, “testing”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2013.png)

Next, I begin testing for vulnerabilities. In the Kali terminal, I switch back to the “python-Code/vulnerabilities” directory (“cd python-Code/vulnerabilities/”). I run “ls” and confirm that xml_vuln.py is present.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2014.png)

Using “cat xml_vuln.py”, I view its contents.

The xml.dom modules are used to create and read DOM trees from XML. The code checks for user‑supplied data.

An xml.sax parser is then initiated, and its configuration enables parsing of external entities.

The highlighted “doc = parseString()” method accepts SAX parse events and converts them into a DOM tree.

With this understanding, I attempt an XML External Entity (XXE) attack — an attack that exploits XML parsers configured to process external entity references.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2015.png)

Before launching the attack, I run a final test to confirm the parser is functioning as expected. I enter: “<author>John > Doe</author>”.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2016.png)

Once again, everything within the tags is displayed (“John > Doe”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2017.png)

Returning to the main XML page, I enter the following syntax, which — if parsed — should enumerate the user and service accounts on the backend server by reading the passwd file as the root user: “<?xml version=”1.0"?> <!DOCTYPE root [<!ENTITY test SYSTEM ‘file:///etc/passwd’>]><root>&test;</root>”. I then click the “Send” button.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2018.png)

After clicking “Send”, the full list of user and service accounts is returned.

## Mitigating XEE Vulnerabilities

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2019.png)

To prevent this type of attack from succeeding again, I return to the Kali terminal and reopen the XML source file (“nano xml_vuln.py”).

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2020.png)

On the line reading “parser.setFeature(feature_external_ges, True)”, I change the boolean value from True to False. This vulnerability was caused by feature_external_ges being enabled, which allowed external entity inclusion. To save the change, I press Ctrl + X, then Y, then Enter.

![Image](https://github.com/darylcbrooks/project-1/blob/project-22/Project%2022%20-%20Step%2021.png)

To verify that the change has been successfully implemented, I use the “grep False xml_vuln.py” command to look for the presence of the “False” boolean value. The results return the full line of code where it is present.

## Conclusion

As we’ve seen, both the Python Pickle module and XML are highly useful tools. However, they must be implemented securely to preserve the confidentiality, integrity, and availability of underlying data. For serialization, JSON provides a safer alternative to Pickle. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉
