Download Link: https://assignmentchef.com/product/solved-cs6500-network-securityassignment-2
<br>
1       Project description

The objective of this project is to implement the various components of a security-enhanced EMAIL system similar to PGP (Pretty Good Privacy) and GnuPG which is based on the OpenPGP standard. The cryptographic libraries from:

<ul>

 <li>OpenSSL (<a href="https://www.openssl.org/">openssl.org</a><a href="https://www.openssl.org/">)</a> (OR)</li>

 <li>Java JCA (<a href="https://docs.oracle.com/javase/7/docs/technotes/guides/security/crypto/CryptoSpec.html">https://docs.oracle.com/javase/7/docs/technotes/guides/security/cry</a>pto/ <a href="https://docs.oracle.com/javase/7/docs/technotes/guides/security/crypto/CryptoSpec.html">html</a><a href="https://docs.oracle.com/javase/7/docs/technotes/guides/security/crypto/CryptoSpec.html">)</a> (OR)</li>

 <li>Python (<a href="https://pypi.org/project/cryptography">https://pypi.org/project/cryptography</a><a href="https://pypi.org/project/cryptography">)</a> can be used</li>

</ul>

<h2>1.1      Encrypted Email</h2>

The task is: Given an email message from User A to User B, message digest algorithm, encryption algorithm and the public/private key pairs of users, generate a security-enhanced output message that will handle: (a) Confidentiality ONLY, (b) Authentication/Integrity ONLY and (c) Authentication/Integrity and Confidentiality. We will assume that for a given set of users, the corresponding public/private key pair is locally available. In reality, the public key information for each user is available from some keyserver and the private key is held securely by each user. For this project, we will assume that Triple-key 3DES, and AES are available. We will assume that only CBC mode is used. The encryption steps are explained in detail in Stallings’ textbook. In order to achieve the different levels of security/privacy, the following mechanisms are used:

Confidentiality ONLY (CONF):

Sender: The email message is encrypted with a randomly generated secret message key (or session key) using a symmetric block cipher algorithm. The message key is encrypted using the receiver’s public key and pre-pended to the message. The [encrypted key, message] is then written into a file that can be sent using traditional email. In essence, given input text M from user A to user B, the transmitted message C is:

<em>C </em>= [<em>E</em><em>KU</em><em>b</em>(<em>K</em><em>s</em><em>,IV</em><em>s</em>)||<em>E</em><em>Kencralgs         </em>(<em>M</em>)]

where Ks is the session key (and any needed initialization vector, IVs) and EKUb is the receiver’s RSA public key. Receiver: The receiver uses its private key to decrypt the secret message key from the message file. This secret key is then fed to the decryption algorithm on the remainder of the message file to extract the plain-text message.

Authentication/Integrity ONLY (AUIN):

Sender: A message digest or hash of the given plain-text message is generated. The digest/hash is then encrypted using the sender’s private key. The [encrypted hash, message] is then written into a file that can be sent using traditional email. In essence, given input text M from user A to user B, the transmitted message C is:

<em>C </em>= [<em>E</em><em>KR</em><em>a</em>(<em>H</em><em>hashlog</em>(<em>M</em>))||<em>M</em>]

where EKRa is the sender’s RSA private key. Receiver: The receiver reads the encrypted hash from the message file and decrypts using the sender’s public key to generate the hash. The digest/hash of the remainder of the message file is computed and compared to the decrypted hash. If they match, success is declared.

Confidentiality and Authentication/Integrity (COAI): Sender: A message digest or hash of the given plaintext message is generated. The digest/hash is then encrypted using the sender’s private key. The [encrypted hash, message] is then encrypted with a randomly generated secret message key (or session key) using a symmetric block cipher algorithm. The message key is encrypted using the receiver’s public key and pre-pended to the message. The [encrypted key, encrypted hash, message] is then written into a file that can be sent using traditional email. In essence, given input text M from user A to user B, the transmitted message C is:

where Ks is the session key (and any needed initialization vector, IVs), Kh is the hashing key (of length equal to the hashing algorithm’s block size), EKRa is sender’s RSA private key and EKUb is receiver’s RSA public key. Receiver: The receiver performs the reverse operation to verify the sender, integrity and to extract the plain-text message.

<h2>1.2      User Interface</h2>

The following are the necessary commands:

<ul>

 <li>./lab2 CreateKeys UserNameListFile RSAKeySize</li>

</ul>

The UserNameListFile will contain a set of users, one per line. RSAKeySize is either 1024 or 2048. For each user, randomly generate an RSA key pair and store private key and public key in user priv keylen.txt and user pub keylen.txt respectively; where keylen is either 1024 or 2048.

<ul>

 <li>./lab2 CreateMail SecType Sender Receiver EmailInputFile EmailOutputFile DigestAlg EncryAlg RSAKey-</li>

</ul>

Size

SecType: CONF, AUIN, COAI are three possible string values for the three cases listed earlier.

Sender/Receiver are sender and recipient of this message.

EmailInputFile contains the input plain-text file (in ASCII format)

EmailOutputFile contains the output of the encryption algorithms (in binary format)

DigestAlg is one of: sha512, sha3-512

EncryAlg is one of: des-ede3-cbc, aes-256-cbc

<ul>

 <li>./lab2 ReadMail SecType Sender Receiver SecureInputFile PlainTextOutputFile DigestAlg EncryAlg Similar to above.</li>

</ul>

<strong>1.3      Output File Format:</strong>

CONF: Line 1: Encrypted Secret Key

Line 2 onwards: Encrypted Data

AUIN: Line 1: Encrypted Hash/Digest Line 2 onwards: Plaintext Data

COAI: Line 1: Encrypted Secret Key

Line 2: Encrypted [Hash/Digest+MessageData]

<h1>2       Starting Point</h1>

<h2>2.1       System Calls</h2>

At the least, the following system calls will be required . On Linux, man command or info command will provide you with relevant information. You can run ’man evp’, ’man rsa’, ’man pem’ for lots of additional information. You should know or learn how to read function interface descriptions from man pages and use them correctly.

Note: By default, the crypto functions produce binary output. You must choose base64 encoded output format.

<h1>3         Sample Session</h1>

Assume that you have created the files lab3.c and the corresponding executables in your LAB2 directory

% cd LAB2 % make lab2

…

% pico Usernames.txt

… file will contain a set of 10-15 users

% ./lab2 CreateKeys Usernames.txt 2048

% ls -l * priv.txt * pub.txt

% pico Mail-sample.txt

… file will contain input text message.

% ./lab2 CreateMail COAI alice bob Mail-sample.txt Mail-out.txt sha512 aes-256-cbc 2048 .. Encrypted File is created.

% ./lab2 ReadMail COAI alice bob Mail-out.txt Mail-decrypt.txt sha512 aes-256-cbc 2048

.. Decryption operation

% diff Mail-sample.txt Mail-decrypt.txt .. Any differences?

<h1>4       What to Submit</h1>

Name your project directory as LAB2 (Note: ALL UPPERCASE) Once you are ready to submit, change directory to the directory above LAB2, and tar all files in the directory with the command:

tar czf Lab2-RollNo.tgz LAB2

Then, submit it online on Moodle. The directory should contain the following files:

<ul>

 <li>Source Files</li>

 <li>Makefile Typing command ‘make’ at the UNIX command prompt, should generate all the required executables.</li>

 <li>A Script file obtained by running UNIX command script which will record the way you have finally tested your program</li>

 <li>a README file containing instructions to compile, run and test your program. The README should document known error cases and weaknesses with the program.</li>

 <li>a COMMENTS file which describes your experience with the project, suggestions for change, and anything else you may wish to say regarding this project. This is your opportunity for feedback, and will be very helpful.</li>

</ul>


