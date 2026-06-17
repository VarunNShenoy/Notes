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







