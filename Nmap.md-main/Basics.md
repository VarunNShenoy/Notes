NMAP uses multiple ways to specify targets, They are:

IP Range - If you want to scan all the IP Addresses from 192.168.0.1 to 192.168.0.10, we can mention 192.168.0.1-10.

IP Subnet using / :  If you want to scan a subnet, you can express it as 192.168.0.1/24, and this would be equivalent to 192.168.0.0-255

Hostname: You can also specify your target by hostname, for example, example.thm

Scanning an Local Network:

In this context, Local refers to the network we are directly connected to, such as an Ethernet or WiFi Network. For example if our IP Address is 192.168.66.69 and we are scanning the whole network. The command used is nmap -sn 192.168.66.0/24.

Because we are scanning the local network, where we are connected via Ethernet or WiFi, we can look up the MAC addresses of the devices. Consequently, we can figure out the network card vendors, which is beneficial information as it can help us guess the type of target device(s). When scanning a directly connected network, Nmap starts by sending ARP requests. When a device responds to the ARP request, Nmap labels it with “Host is up”.

Scanning a Remote Network:

In this context, “remote” means that at least one router separates our system from this network. As a result, all our traffic to the target systems must go through one or more routers. Unlike scanning a local network, we cannot send an ARP request to the target.

Our system has the IP address 192.168.66.89 and belongs to the 192.168.66.0/24 network. In the terminal below we scan the target network 192.168.11.0/24 where there are two or more routers (hops) separate our local system from the target hosts.


Types of scan:

-sn : Ping scan - to discover live hosts.
-sT: 










