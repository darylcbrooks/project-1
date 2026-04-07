### Click Header Image or Project Title to Watch The Video
[![Watch the video](https://img.youtube.com/vi/OMsnDnn4ifU/0.jpg)](https://youtu.be/OMsnDnn4ifU)

### [Project 5 - Obfuscation pt 1 - Steganography and Symmetric Encryption](https://youtu.be/OMsnDnn4ifU)

This will be the first half of my project, where I explore different obfuscation techniques designed to protect the confidentiality of information. So, what exactly is obfuscation? It’s the deliberate act of making code, data, or communication difficult to understand and analyze. Encryption and encoding are two very popular methods of obfuscation. So, let’s dive in and explore some specific methods of obfuscation and how they work:

## Steganography With Steghide

The first obfuscation method I explore in this project is steganography, which is the concealment of information within computer files. In the example below, I’m going to use an application known as Steghide to hide written communication behind an image file.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2001.png)

To start things off, I need to change directories to where the image file is stored (Desktop/Challenges/Steganography).

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2002.png)

To see the contents of the clue.jpg image that I will be working with, I execute the eog clue.jpg command. Now, let’s see what our tiny sleuth is hiding.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2003.png)

The following command uses the Steghide application to extract any hidden data from the image, and upon entering a known passphrase, it saves any found output to a file called hidden.txt (steghide extract -sf clue.jpg).

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2004.png)

Now that the hidden.txt file has been saved to my local working directory, I’m going to use the cat command to display its contents to see if the image file was hiding anything (cat hidden.txt). It appears that it was hiding the word “Steganography.” This was a very simplistic example of how steganography works, but in reality, insider threats such as employees colluding in a fraudulent scheme, can use file transfers with images like this to hide their communications by using unorthodox channels that aren’t as easy to trace.

## Symmetric Encryption With OpenSSL

Next, I’ll be exploring different symmetric algorithms that can be used to obfuscate plain text documents behind ciphertext. Symmetric encryption uses a single key to both encrypt and decrypt data. It’s considered less secure than asymmetric encryption; however, it’s also regarded as more efficient due to utilizing less processing power.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2005.png)

To access the files I will be using to demonstrate how symmetric encryption works, I need to switch from the Steganography subdirectory to the Cryptography/Symmetric subdirectory. The cd ../Cryptography/Symmetric command allows me to do so.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2006.png)

Once in the appropriate subdirectory, using the ls command lists all the files stored in it. SPOILER ALERT: Each file contains lines of ciphertext encrypted in the respective symmetric algorithm it’s named after, and when decrypted, all translate to the word “Cryptography.” END SPOILER

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2007.png)

I’m next executing a command (awk '{print}' * > salted.txt) that takes the output of all the files in this subdirectory and saves them to a single text file called salted.txt. This will allow me to display the contents of each file all at once, saving me the time it would take to use the cat command on each file individually.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2008.png)

Now, all I have to do is use the cat command once with the salted.txt file to display the ciphertext in each of the six files stored in the subdirectory. We can see that the output varies for each file regardless of them all containing the same cleartext input (as mentioned in my spoiler alert). You may have noticed that the term “salt” has been used for both the naming convention and output shown in the image above. This highlights the usage of the salting technique in the obfuscation of text. Salting is a technique used to add extra characters to ciphertext to add another layer of complexity to the output. For example: Let’s say the cleartext phrase being obfuscated here was “my password is apple.” If an adversary were executing a dictionary brute-force attack against the salted.txt file in an attempt to decipher its contents, most orthodox logic would conclude that there’s a fairly high likelihood of this ciphertext being cracked because every word of cleartext it obfuscates is a common dictionary term. However, salting may add characters such as a4ty to the beginning of “my password is apple,” resulting in ciphertext for a4ty my password is apple, which isn’t as easily solvable.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2009.png)

In this next phase of the project, I use a software application known as OpenSSL to decrypt each of the six files in the subdirectory and save the decrypted output to a respectively named text file for us to view the cleartext term stored in it. OpenSSL is a command-line utility used to generate private keys, encrypt, decrypt, create certificate signing requests, generate security certificates, and identify certificate information. Starting off, I use OpenSSL to decrypt (openssl enc -d -des-ecb -in des-esb.enc -out des-ecb.txt) a file encrypted with the Data Encryption Standard algorithm in Electronic Codebook mode (DES-ECB). DES-ECB is an outdated, insecure block cipher method that divides data into 64-bit blocks, encrypting each independently with the same key. Because identical plaintext blocks produce identical ciphertext blocks, it reveals data patterns. It is no longer recommended due to its vulnerability to brute-force attacks.

Notice that I’m not being asked for a public or private key to decrypt this file, only a single password. While convenient, the usage of a single key can be considered a single point of failure, meaning that if the symmetric key were to fall into the wrong hands, a malicious actor would have the ability to not only encrypt files with it but also decrypt files previously encrypted with it.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2010.png)

Now that I’ve decrypted the contents of des-ecb.enc and saved them in the des-ecb.txt text file, I use the cat command to display the cleartext (cat des-ecb.txt), which returns a value of “Cryptography.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2011.png)

Next, I use OpenSSL to decrypt (openssl enc -d -des-cbc -in des-cbc.enc -out des-cbc.txt) a file encrypted with the Data Encryption Standard algorithm in Cipher Block Chaining mode (DES-CBC). DES-CBC is a symmetric-key block cipher that encrypts 64-bit blocks of data using a 56-bit key (with 8 parity bits). Each plaintext block is XORed with the previous ciphertext block before encryption, ensuring that identical plaintext blocks produce different ciphertext.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2012.png)

Now that I’ve decrypted the contents of des-cbc.enc and saved them in the des-cbc.txt text file, I use the cat command to display the cleartext (cat des-cbc.txt), which returns a value of “Cryptography.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2013.png)

Moving on, I use OpenSSL to decrypt (openssl enc -d -des-ede3-ecb -in 3des-ecb.enc -out 3des-ecb.txt) a file encrypted with the Triple Data Encryption Standard in Electronic Codebook mode (3DES-ECB). 3DES-ECB is a deprecated, insecure block cipher method that applies the DES algorithm three times to each 64-bit data block independently. Because identical plaintext blocks produce identical ciphertext, it is vulnerable to pattern analysis and attacks.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2014.png)

Now that I’ve decrypted the contents of 3des-ecb.enc and saved them in the 3des-ecb.txt text file, I use the cat command to display the cleartext (cat 3des-ecb.txt), which returns a value of “Cryptography.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2015.png)

Next up, I use OpenSSL to decrypt (openssl enc -d -des-ede3-cbc -in 3des-cbc.enc -out 3des-cbc.txt) a file encrypted with the Triple Data Encryption Standard in Cipher Block Chaining mode (3DES-CBC). 3DES-CBC is a legacy symmetric block cipher that applies DES three times per 64-bit block with a 112-bit or 168-bit key. It is now deprecated, insecure, and forbidden by NIST for new applications due to vulnerabilities.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2016.png)

Now that I’ve decrypted the contents of 3des-cbc.enc and saved them in the 3des-cbc.txt text file, I use the cat command to display the cleartext (cat 3des-cbc.txt), which returns a value of “Cryptography.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2017.png)

Making my way down the list, I use OpenSSL to decrypt (openssl enc -d -aes-256-ecb -in aes-ecb.enc -out aes-ecb.txt) a file encrypted with the Advanced Encryption Standard in Electronic Codebook (AES-ECB) mode. AES-ECB is the simplest and fastest, but least secure block cipher mode, where each 128-bit block is encrypted independently using the same key. It is highly vulnerable to pattern analysis, as identical plaintext blocks produce identical ciphertext blocks. Due to these security flaws, it is generally not recommended for encrypting data larger than one block.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2018.png)

Now that I’ve decrypted the contents of aes-ecb.enc and saved them in the aes-ecb.txt text file, I use the cat command to display the cleartext (cat aes-ecb.txt), which returns a value of “Cryptography.”

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2019.png)

Last but certainly not least, I use OpenSSL to decrypt (openssl enc -d -aes-256-cbc -in aes-cbc.enc -out aes-cbc.txt) a file encrypted with the Advanced Encryption Standard in Cipher Block Chaining (AES-CBC) mode. AES-CBC is a symmetric block cipher mode for AES that enhances security by chaining blocks, where each ciphertext block depends on all preceding plaintext blocks. It requires a unique, random Initialization Vector for the first block to ensure identical plaintexts produce different ciphertexts. It is widely used for data confidentiality but lacks built-in integrity, making it vulnerable to padding oracle attacks.

![Image](https://github.com/darylcbrooks/project-1/blob/project-5/Project%205%20-%20Step%2020.png)

Now that I’ve decrypted the contents of aes-cbc.enc and saved them in the aes-cbc.txt text file, I use the cat command to display the cleartext (cat aes-cbc.txt), which returns a value of “Cryptography.”

## Conclusion

This concludes part 1 of my obfuscation project. Hopefully, this has provided a better understanding of how steganography and symmetric encryption work. Specifically with symmetric encryption, we can see that there are a variety of algorithms to choose from, all with inherent strengths and weaknesses. However, it is strongly recommended to use algorithms in the AES family due to their overall strength, as the others showcased in this project are legacy technologies. You can watch the video walkthrough of this same project at the top of page. Until next time, take care of yourself and…SHOW YOUR WORK!!! 😉

