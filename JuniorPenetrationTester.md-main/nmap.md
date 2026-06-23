Nmap, short for Network Mapper, is free, open-source software released under the GPL license, created by Gordon Lyon (Fyodor), a network security expert and open-source programmer. Nmap is an industry-standard tool for mapping networks, identifying live hosts, and discovering running services. Nmap’s scripting engine can further extend its functionality, from fingerprinting services to exploiting vulnerabilities. A Nmap scan usually goes through the steps shown in the figure below, although many are optional and depend on the command-line arguments you provide.

![NMAP_SCAN](nmap_steps.png)

**Subnetworks**

![subnetworks](NMAP_SUBNETWORKS.png)

The figure above shows two types of subnets:

1. Subnets with /16, which means that the subnet mask can be written as 255.255.0.0. This subnet can accommodate around 65,000 hosts.
2. Subnets with /24, which indicates that the subnet mask can be expressed as 255.255.255.0. This subnet can have around 250 hosts.

Questions: 

1. How many devices can see the ARP Request? --> 4
2. Did computer6 receive the ARP Request? (yea/nay)--> nay
3. How many devices can see the ARP Request? --> 4
4. Did computer6 reply to the ARP Request? (yea/nay) --> yea


**NMAP BASIC PORT SCANS**

**TCP and UDP Ports**

A port is usually linked to a service using that specific port number. For instance, an HTTP server would bind to TCP port 80 by default; if it supports SSL/TLS, it would also listen on TCP port 443. (TCP ports 80 and 443 are the default ports for HTTP and HTTPS; however, the web server administrator might choose other port numbers if necessary.) Furthermore, no more than one service can listen on any TCP or UDP port (on the same IP address).

![Understanding_Network_Ports](Open-Closed_ports.png)

Ports can be classified into: 

1. An open port indicates that a service is listening on that port.
2. A closed port indicates that no service is listening on that port.

However, in practical situations, we need to consider the impact of firewalls. For instance, a port might be open, but a firewall might be blocking the packets. Therefore, Nmap considers the following six states:

1. An open port indicates that a service is listening on the specified port.
2. A closed port indicates that no service is listening on the specified port, although the port is accessible. By accessible, we mean that it is reachable and is not blocked by a firewall or other security appliances/programs.
3. Filtered means that Nmap cannot determine whether the port is open or closed because it is not accessible. This state is usually due to a firewall preventing Nmap from reaching that port. Nmap’s packets may be blocked from reaching the port; alternatively, responses may be blocked from reaching Nmap’s host.
4. Unfiltered means that Nmap cannot determine whether the port is open or closed, even though the port is accessible. This state is encountered when using an ACK scan -sA.
5. Open|Filtered: This means that Nmap cannot determine whether the port is open or filtered.
6. Closed|Filtered: This means that Nmap cannot decide whether a port is closed or filtered.

**Questions:**

Which service uses UDP port 53 by default? --> DNS
Which service uses TCP port 22 by default?--> SSH
How many port states does Nmap consider? --> 6
Which port state is the most interesting to discover as a pentester? --> Open

**TCP Flags**

Nmap supports different types of TCP Port Scans.

TCP Header Review

![TCP_Header](TCP_Header.png)

TCP Flags are captured in red in the below image:

1. URG: Urgent flag indicates that the urgent pointer field is significant. The urgent pointer indicates that the incoming data is urgent, and that a TCP segment with the URG flag set is processed immediately, without waiting for previously sent TCP segments.
2. ACK: Acknowledgement flag indicates that the acknowledgement number is significant. It is used to acknowledge the receipt of a TCP segment.
3. PSH: Push flag asking TCP to pass the data to the application promptly.
4. RST: The reset flag is used to reset the connection. Another device, such as a firewall, might send it to tear a TCP connection. This flag is also used when data is sent to a host, and there is no service on the receiving end to answer.
5. SYN: The synchronise flag is used to initiate a TCP 3-way handshake and synchronise sequence numbers with the other host. The sequence number should be set randomly during TCP connection establishment.
6. FIN: The sender has no more data to send.

Questions: 

What 3 letters represent the Reset flag?--> RST
Which flag needs to be set when you initiate a TCP connection (first packet of TCP 3-way handshake)?--> SYN


**TCP Connect Scan:**

TCP connect scan works by completing the TCP 3-way handshake. In standard TCP connection establishment, the client sends a TCP packet with the SYN flag set, and the server responds with SYN/ACK if the port is open; finally, the client completes the 3-way handshake by sending an ACK.

We are interested in learning whether the TCP port is open, not in establishing a TCP connection. Hence, the connection is torn as soon as its state is confirmed by sending a RST/ACK. You can choose to run a TCP connect scan using -sT.

![NMAP TCP SCAN](NMAP_TCP_SCAN.png)

It is important to note that if you are not a privileged user (root or sudoer), a TCP connect scan is the only possible option to discover open TCP ports.

In the following Wireshark packet capture window, we see Nmap sending TCP packets with the SYN flag set to various ports, 5900, 22, 80, and so on. By default, Nmap will attempt to connect to the 1000 most common ports. A closed TCP port responds to a SYN packet with RST/ACK to indicate that it is not open. This pattern will repeat for all the closed ports as we attempt to initiate a TCP 3-way handshake with them.

![NMAP_WIRESHARK_RESULTS](NMAP_WIRESHARK_RESULTS.png)

We notice that port 80 is open, so it replied with a SYN/ACK, and Nmap completed the 3-way handshake by sending an ACK. The figure below shows all the packets exchanged between our Nmap host and the target system’s port 80. The first three packets are the TCP 3-way handshake. Then the fourth packet tears it down with an RST/ACK.

Questions: 

What is the state of the FTP service running on port 21? --> Open
What is Nmap’s guess about the service running on port 53? --> domain

![Scan_results](Scan_results_nmap.png)

**TCP SYN Scan**

Unprivileged users are limited to the connect scan. However, the default scan mode is a SYN scan, and it requires a privileged (root or sudo) user to run. SYN scan does not need to complete the TCP 3-way handshake; instead, it tears down the connection after receiving a response from the server. Because we didn’t establish a TCP connection, the scan is less likely to be logged. We can select this scan type by using the -sS option. The figure below shows how the TCP SYN scan works without completing the TCP 3-way handshake.

![SYN_SCAN](NMAP_TCP_SCAN.png)

![TCP_SCAN](NMAP_WIRESHARK_RESULTS.png)

To better see the difference between the two scans, consider the following screenshot. In the upper half of the following figure, we can see TCP connect scan -sT traffic. Any open TCP port will require Nmap to complete the TCP 3-way handshake before closing the connection. In the lower half of the following figure, we see how a SYN scan -sS does not need to complete the TCP 3-way handshake; instead, Nmap sends an RST packet once a SYN/ACK packet is received.


























   


