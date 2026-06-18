**Telnet:**

The Telnet protocol is an application-layer protocol used to connect to a virtual terminal of another computer. Using Telnet, a user can log into a remote machine and access its terminal (console) to run programs, start batch processes, and perform system administration tasks remotely.

The Telnet protocol is relatively simple. When a user connects, they are asked for a username and password. Upon correct authentication, the user gains access to the remote system's terminal. However, all communication between the Telnet client and the Telnet server is unencrypted, making it an easy target for attackers.

Telnet Today

Telnet was widely used for remote administration in the early days of networking. However, it has been almost entirely replaced by SSH for interactive remote access. It is very unlikely to find telnet enabled on modern, properly configuted systems. However it has been completly replaced by SSH for interactive remote access.

Telnet Client as a testing tool:

While Telnet servers are rare, the Telnet client remains useful as a simple tool for connecting to any TCP port and manually interacting with text-based protocols.

How telnet works : 

A Telnet server listens for incoming connections on port 23 using the Telnet protocol. The following terminal output is illustrative and was captured from a separate environment. The Telnet port is not open on the target VM attached to this room, so you will not be able to replicate this specific connection. However, it demonstrates the authentication flow clearly.

The steps are as follows:

1. The user is asked to provide their login name (username). In this example, the user enters frank.
2. They are then asked for the password, D2xc9CgD. The password is not shown on the screen; however, it is displayed below for demonstration purposes.
3. Once the system checks the login credentials, the user is greeted with a welcome message.
4. The remote server grants a command prompt, frank@bento:~$. The $ indicates that this is not a root terminal.

Why Telnet is Insecure

Telnet is no longer considered a secure option. Anyone capturing your network traffic can discover your usernames and passwords, which would grant them access to the remote system. This includes:

1. Attackers on the same network segment
2. Anyone who has compromised a router or switch along the path
3. Malicious insiders with network access
4. Anyone performing a successful man-in-the-middle attack

Questions:

To which port will the telnet command with the default parameters try to connect? --> 23

**Hypertext Transfer Protocol (HTTP)**

Hypertext Transfer Protocol (HTTP) is the protocol used to transfer web pages. Your web browser connects to the web server and uses HTTP to request HTML pages, images, and other files. It also submits forms and uploads various files. Any time you browse the World Wide Web (WWW), you are using the HTTP protocol.

HTTP vs HTTPS

HTTP sends and receives data as cleartext (not encrypted). This means anyone with access to the network traffic can read the content being transferred, including sensitive information like login credentials and personal data.

Today, the vast majority of websites use HTTPS (HTTP Secure), which wraps HTTP inside TLS encryption. Modern browsers mark plain HTTP sites as "Not Secure", and some features (like geolocation and camera access) are blocked entirely on non-HTTPS sites. However, understanding how HTTP works is still essential because:

1. The HTTP commands and structure are identical whether using HTTP or HTTPS.
2. You will encounter HTTP during internal penetration tests and on legacy systems.
3. Understanding the protocol helps you identify and exploit web vulnerabilities.
4. Tools like Burp Suite decrypt HTTPS traffic for analysis, showing you raw HTTP.
F
or this demonstration, plain HTTP is used so you can see exactly what is being transmitted.


Manually Sending HTTP Requests

Because HTTP is a cleartext protocol, you can use a simple tool such as Telnet (or Netcat) to communicate with a web server and act as a "web browser". The key difference is that you need to input the HTTP-related commands instead of the web browser doing that for you.

In the following example, you will see how to request a page from a web server and discover the web server version. The Telnet client is used because Telnet is a simple protocol that uses cleartext for communication. The steps are as follows:

1. Connect to port 80 using telnet 10.64.159.224 80.
2. Type GET /index.html HTTP/1.1 to retrieve the page index.html, or GET / HTTP/1.1 to retrieve the default page.
3. Provide a value for the host header, such as host: telnet, and press the Enter/Return key twice.

In the console output below, the requested page is recovered along with information not usually displayed by the web browser. If the requested page is not found, the server returns error 404.

Information Revealed in HTTP Headers

1. Specific versions may have known vulnerabilities you can research.
2. The OS information helps tailor further attacks.
3. Even knowing the web server software narrows down potential attack vectors.

An HTTP server (web server) and an HTTP client (web browser) are required to use the HTTP protocol. The web server "serves" a specific set of files to the requesting web browser.

Popular choices for HTTP servers include:

1. Nginx has become the most widely used web server on the internet, known for its performance and efficiency in handling concurrent connections. It is free and open-source.
2. Apache remains extremely popular and powers a large portion of websites. It is highly configurable with a vast ecosystem of modules. It is also free and open-source.
3. Internet Information Services (IIS) is Microsoft's web server, commonly found in Windows enterprise environments. It requires a Windows Server licence.

Other notable web servers include LiteSpeed, Caddy (which has automatic HTTPS built in), and Node.js for JavaScript-based applications.

Questions: 

Launch the attached VM. From the AttackBox terminal, connect using Telnet to 10.64.159.224 80 and retrieve the file flag.thm. What does it contain?

Connect telnet <IP Address> 80
GET /flag.thm HTTP/1.1
host: telnet

<img width="658" height="323" alt="image" src="https://github.com/user-attachments/assets/3b73e1c1-8208-4f81-9975-cdaf31e0cd3c" />

**File Trasfer Protocol** 

File Transfer Protocol (FTP) was developed to make the transfer of files between different computers with different systems efficient. It was one of the earliest protocols designed for the internet and remains in use today, though it has largely been replaced by secure alternatives for most purposes.

Modern FTP

FTP sends credentials and data in cleartext, making it insecure for transferring sensitive information. For this reason, FTP has been replaced in most environments by:

1. SFTP (SSH File Transfer Protocol) runs over SSH on port 22 and encrypts all traffic. This is the most common replacement for FTP.
2. FTPS (FTP Secure) adds TLS encryption to the FTP protocol on port 990 (implicit TLS) or uses STARTTLS on port 21.
3. SCP (Secure Copy Protocol) also runs over SSH, though it is being deprecated in favour of SFTP.

Manually interacting with FTP:

FTP sends and receives data as cleartext, so you can use Telnet to communicate with an FTP Server and act as FTP Client.

1. A connection was made to an FTP server using a Telnet client. Since FTP servers listen on port 21 by default, the Telnet client was directed to connect to port 21 instead of the default Telnet port.
2. The username was provided with the command USER frank.
3. The password was provided with the command PASS D2xc9CgD.
4. Because the correct username and password were supplied, login succeeded.

A command like STAT can provide additional information. The SYST command shows the System Type of the target (UNIX in this case). PASV switches the mode to passive. It is worth noting that there are two modes for FTP:

1. Active: In active mode, the data is sent over a separate channel originating from the FTP server's port 20. The server initiates the data connection back to the client. This often fails when the client is behind a firewall or NAT.
2. Passive: In passive mode, the data is sent over a separate channel originating from an FTP client's port above port number 1023. The client initiates both connections. This is more firewall-friendly and is the default for most modern FTP clients.

The comamnd TYPE A switches the file transfer to ASCII Mode, while TYPE 1 switches the file transfer mode to binary. However, file transfer cannot be completed using a simple client such as Telnet because FTP creates a seperate connection for data transfer.

Notice in the STAT output that the server explicitly states "Control connection is plain text" and "Data connections will be plain text". This confirms that everything, including credentials, is transmitted without encryption.

Difference between ASCII mode and binary mode in ftp via telnet

When using FTP via Telnet, you can switch between ASCII mode and binary mode depending on the type of data you’re transferring. The key difference lies in how the data is interpreted and transmitted.

ASCII Mode: Used for text files (e.g., .txt, .html, .csv, source code files).

How it works:

1. Transfers data as characters (text).
2. Performs character encoding conversion if needed (e.g., different newline formats between systems).

Binary Mode: Used for non-text files, such as jpeg, executables etc.

How it works

1. Transfers data byte-by-byte exactly as is.
2. No conversion or interpretation is performed.

|  Feature | ASCII Mode | Binary Mode |
|----------|------------|-------------|
| Data type | All Text files | All file types |
|Conversion| Yes (line endings, encoding) |No conversion at all|
| Risk of Corruption | Yes (for non-text files) | No|
| Transfer method | Character-based | Byte-Byte|
| Default use case | .txt, .html | .jpeg,.exe |

Using FTP CLient :

Instead of using the telnet client if you need to run the ftp client - Execute the command ftp <IP Address>

After logging in successfully, you get the FTP prompt, ftp>, to execute various FTP commands. In the example below, ls lists the files, ascii switches to ASCII mode since the target is a text file (not binary), and get FILENAME initiates the file transfer by establishing a separate data channel.


<img width="887" height="528" alt="image" src="https://github.com/user-attachments/assets/f7393a31-3ba3-43ca-84f6-4b50e7f0d2fe" />

Annonymous FTP:

Some FTP servers allow anonymous login, typically using the username anonymous or ftp with any email address as the password (or no password at all). Anonymous FTP was historically used for public file distribution, such as software downloads and documentation. During penetration testing, always try an anonymous login when you discover an FTP server.

Anonymous FTP servers might contain sensitive files that were accidentally exposed, configuration backups, or provide a way to upload malicious files if write access is enabled.

FTP Servers and Clients
There are various FTP server software options available:

1. vsftpd (Very Secure FTP Daemon) is one of the most common FTP servers on Linux systems.
2. ProFTPDis highly configurable and modular.
3. Pure-FTPd(opens in new tab) focuses on security and simplicity.
4. On Windows, IIS includes FTP server capabilities.
   
For FTP clients, in addition to the console FTP client commonly found on Linux systems, you can use a GUI-based client such as FileZilla. Note that major web browsers have removed FTP support in recent years, so browser-based FTP access is no longer available.

Security Implications:

Because FTP sends login credentials, commands, and files in cleartext, FTP traffic is an easy target for attackers. Anyone capturing network traffic can see usernames and passwords, file contents being transferred, directory listings revealing server structure, and the commands showing what actions users are performing. If you must use FTP, restrict it to isolated networks or use FTPS with TLS encryption. For most purposes, SFTP over SSH is the recommended alternative

Questions:

1. For this task, use the FTP client on the AttackBox to connect to 10.65.151.200. The credentials are frank / D2xc9CgD.

<img width="655" height="404" alt="image" src="https://github.com/user-attachments/assets/6f3c80c3-5a87-409d-b3b6-cf29abdea8fb" />

<img width="651" height="257" alt="image" src="https://github.com/user-attachments/assets/c9a1e23c-e113-4b5d-af0d-c3785565a7ae" />

Flag: THM{364db6ad0e3ddfe7bf0b1870fb06fbdf}

**Simple Mail Transfer Protocol:**

Email is one of the most used services on the Internet. There are various configurations for email servers; for instance, you may set up an email system to allow local users to exchange emails with each other with no access to the Internet. However, this task considers the more general setup where different email servers connect over the Internet.

Email Delivery Components

Email delivery over the Internet requires the following components:

1. Mail User Agent (MUA): The email client (e.g., Thunderbird, Outlook, a webmail interface).
2. Mail Submission Agent (MSA): Receives mail from the MUA, checks for errors, and forwards it.
3. Mail Transfer Agent (MTA): Routes and delivers mail between servers.
4. Mail Delivery Agent (MDA): Stores the email in the recipient's mailbox for retrieval.

<img width="630" height="329" alt="image" src="https://github.com/user-attachments/assets/e7186e51-4c2c-42c5-962b-68299aad48fd" />

The figure shows the following five steps that an email needs to go through to reach the recipient's inbox:

1. The MUA has an email message to be sent. It connects to the MSA to submit the message.
2. The MSA receives the message, checks for any errors before transferring it to the MTA, which is commonly hosted on the same server.
3. The MTA sends the email message to the MTA of the recipient. The MTA can also function as an MSA.
4. A typical setup has the MTA server also functioning as the MDA.
5. The recipient collects their email from the MDA using their email client (MUA).

If the above steps sound confusing, consider the following analogy:

1. You (MUA) want to send postal mail.
2. The post office employee (MSA) checks the postal mail for any issues before your local post office (MTA) accepts it.
3. The local post office checks the mail destination and sends it to the post office (MTA) in the correct country.
4. The post office (MTA) delivers the mail to the recipient's mailbox (MDA).
5. The recipient (MUA) regularly checks the mailbox for new mail. They notice the new mail and take it.

Email Protocols

In the same way you follow a protocol to communicate with an HTTP server, you rely on email protocols to talk with an MTA and an MDA. The protocols are:

1. Simple Mail Transfer Protocol (SMTP) for sending email
2. Post Office Protocol version 3 (POP3) or Internet Message Access Protocol (IMAP) for receiving email

SMTP is explained in this task. POP3 and IMAP are covered in the following two tasks.

Simple Mail Transfer Protocol (SMTP) is used to communicate with an MTA server. The original SMTP uses cleartext, where all commands are sent without encryption. However, modern email infrastructure uses several ports with different security models:

1. Port 25 is the traditional SMTP port used for server-to-server communication (MTA to MTA). It is often blocked by ISPs for residential connections to prevent spam. On port 25, encryption is optional and negotiated via STARTTLS.
2.  Port 587 is the submission port, used by email clients (MUA) to submit messages to their mail server (MSA). This is the recommended port for sending email and typically requires authentication. TLS encryption is negotiated via the STARTTLS command.
3.  Port 465 was originally designated for SMTPS (SMTP over implicit TLS), then deprecated, and has since been reinstated. On this port, TLS encryption begins immediately upon connection.

Manual Sending Email via Telnet

Because SMTP can use cleartext, you can use a basic Telnet client to connect to an SMTP server and act as an email client (MUA), sending a message. Once connected, issue helo hostname (or ehlo hostname for extended SMTP) and then start composing the email.

After helo, the commands mail from: and rcpt to: indicate the sender and the recipient. When the email message is ready, the data command begins the message body. The message is ended by typing a period on a line by itself (. followed by Enter). The SMTP server then queues the message.

**Email Spoofing:**

Notice something important in the example above: the "from" address was specified manually, and the server accepted it without verifying that the sender actually controls that email address. This is how email spoofing works. SMTP was designed in an era of trusted networks and has no built-in mechanism to verify sender identity.

Security Implications

Understanding SMTP is important for security professionals because:

1. Email remains the primary vector for phishing attacks.
2. Misconfigured mail servers can be used as open relays for spam.
3. Cleartext SMTP exposes email content and credentials to network sniffing.
4. Knowledge of SMTP helps you understand email header analysis during incident response.

During penetration tests, you might test for open relay configurations, attempt email spoofing to assess security awareness, or analyse email headers to trace the origin of suspicious messages.

Questions:

Using the AttackBox terminal, connect to the SMTP port of the target VM. What is the flag that you can get?


THM{5b31ddfc0c11d81eba776e983c35e9b5}

Post Office Protocol 3 (POP3)

Post Office Protocol version 3 (POP3) is a protocol used to download email messages from a Mail Delivery Agent (MDA) server, as shown in the figure below. The mail client connects to the POP3 server, authenticates, downloads the new email messages, and then (optionally) deletes them from the server.

![POP3 Working](POP3.png)

POP3 Ports and Encryption

Like other protocols covered in this room, POP3 was designed without encryption:

Port 110 is the default POP3 port using cleartext. Some servers support upgrading the connection to TLS using the STLS command (similar to STARTTLS in SMTP).
Port 995 is used for POP3S (POP3 over implicit TLS). The connection is encrypted from the start.

Most email providers today require or strongly encourage POP3S on port 995. However, you may still encounter plaintext POP3 on internal networks, legacy systems, or misconfigured servers.

POP3 Commands

| Command | Description |
|---------|-------------|
| USER username | Identifies the user|
| Pass password | Authenticates with the password |
| STAT | Returns the number of messages and the total size |
| LIST | Lists all messages with their sizes |
| RETR n | Retrieves message number n |
| DELE n | Marks message n for deletion |
| RSET |Resets (unmarks) messages marked for deletion |
| QUIT | Ends the session and deletes marked messages |


POP3 Behaviour: Download and Delete

Based on the default settings, the mail client deletes the mail message after it downloads it. This "download and delete" model means:

1. Emails are stored locally on your device, not on the server.
2. Once downloaded, the email is only accessible from that specific device.
3. If your device is lost or damaged, the emails are gone (unless backed up).
4. Storage on the mail server is minimised.

POP3 vs IMAP: When to Use Each

POP3 is still useful in specific scenarios:

1. When you want to access email offline and have limited or unreliable internet connectivity
2. When you need to minimise server-side storage
3. When you only access email from a single device
4. For archiving emails locally

However, IMAP has largely replaced POP3 for most users because of its synchronisation capabilities.

Security Implications

From a security perspective, finding a POP3 server (especially on port 110) during a penetration test presents opportunities. Credentials sent over cleartext POP3 can be captured through network sniffing, password attacks can be conducted against POP3 authentication, and successful access to a mailbox may reveal sensitive information, credentials for other systems, or password reset links.

Questions: 

1. Connect to the VM (10.66.149.42) at the POP3 port. Authenticate using the username frank and password D2xc9CgD. What is the response you get to STAT? --> +OK 0 0

![POP3 Answeres](POP3_Answers.png)

3. How many email messages are available to download via POP3 on 10.66.149.42? --> 0

STAT results give you the answer count to be zero
























