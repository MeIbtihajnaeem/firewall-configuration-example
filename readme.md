# Opnsense Firewall Configuration Readme

## Introduction
I am configuring Opnsense as the firewall, utilizing Ubuntu and Debian. Ubuntu as Server and Debian as client within VirtualBox virtual machines. To achieve network segmentation, I've established two distinct Host-only networks: the first named InternalNetwork and the second named ClientNetwork.

Within the VirtualBox setup, Ubuntu Server and Opnsense reside on InternalNetwork, while Debian Client and Opnsense are positioned on ClientNetwork. Consequently, the Ubuntu Server becomes inaccessible from Debian Client directly, necessitating the use of the Opnsense firewall to facilitate communication between them.

The Opnsense firewall is configured with specific rules outlined as follows:
1. All TCP and  UDP requests are denied by default except.
2. TCP requests are exclusively permitted on ports 8081 and 8082..
3. UDP requests are exclusively allowed on port 8083.
4. Enable SSH access to the Server through the firewall, specifically using port 27016.
5. Allow any ICMP request 


## TestBed Configurations
In the described virtualized environment with three virtual machines and two interfaces (InternalNetwork and ClientNetwork), the assigned IP addresses are as follows:
1. Opnsense:
   - InternalNetwork Interface: 192.168.56.2
   - ClientNetwork Interface: 192.168.2.2
2. Ubuntu Server:
   - InternalNetwork Interface: 192.168.56.6 (static)
3. Debian Client: 
   - ClientNetwork Interface: 192.168.2.13 (dynamic)
This addressing scheme delineates the network configuration, enabling communication and interaction between Opnsense, the Ubuntu Server, and the Debian Client through the specified interfaces and IP addresses.

## Configuration of Virtual Box
I've established two distinct networks using the VirtualBox Network Manager. These networks are entirely segregated and are named InternalNetwork and ClientNetwork.

The ClientNetwork is defined with an IP address range from 192.168.2.1 as the Lower Bound to 192.168.2.254 as the Upper Bound. On the other hand, the InternalNetwork has an IP address range spanning from 192.168.56.1 (Lower Bound) to 192.168.56.254 (Upper Bound).


## Configuration of Firewall Interface & IP Address
Upon starting the Opnsense VM, the initial task post-login involves the assignment of interfaces and allocation of IP addresses. This process is crucial, especially considering the configuration of two adapters specified in the Opnsense network settings.


Both adapters are configured as Host-only networks. The first, designated as Network 1, serves as a private network connecting the Server and Opnsense. The others function as a public network accessible to clients. To manage this network segregation effectively, two interfaces are established on the firewall: the first is LAN, representing the private network, and the second is OPT1, serving as a distinct public network.

This configuration ensures that client access to the Server necessitates interaction with the firewall. After configuring these interfaces and assigning IP addresses, the Opnsense web GUI interface becomes accessible through the LAN interface. The visual representation of this setup is depicted in the image below.


## Configuration of Debian Client and Server
The Debian Client and Ubuntu Server are basic virtual machines running the Ubuntu and Debian operating system. The Client machine is equipped with Netcat, Web Browser and Wireshark to facilitate the demonstration of the firewall's functionality. On the other hand, the Server is configured to operate a straightforward SSH using OpenSSH on port 27016. This setup allows for a practical showcase of the firewall in action and the Server's functionality.


## Firewall Configuration and Rules
Below are all the mentioned firewall rules. I've configured them, and the following provides an explanation for each rule, one by one.

### Rule for ICMP:
This rule states that all ICMP requests are permissible.
Terminal Result
WireShark Output (Passed)

### Rule for TCP Requests:
This rule state that no TCP request on any port is allowed accept for port 8081 to 8082
Wireshark Output for port 8080 (Rejected)
There were unsuccessful attempts to establish a TCP connection from 192.168.2.1 to 192.168.2.2 on port 8080, resulting in connection resets (RST, ACK). 

Wireshark Output for port 8081 and 8082 (Passed)
The new request involves an initial TCP connection establishment from 192.168.2.1 to 192.168.2.2 on port 8081

### Rule for UDP Requests
Wireshark Output for port 8082 (Rejected)
The utilization of port 8082 for UDP requests promptly results in a "Destination unreachable" message indicating that port 8082 is not allowed to pass on UDP

Wireshark Output for port 8083 (Passed)
The utilization of port 8082 for UDP requests promptly results in a "Destination unreachable" message indicating that port 8082 is not allowed to pass on UDP. However, when employing port 
8083, this error is not explicitly indicated. Nonetheless, no further output is received, as UDP does not acknowledge the request.

### Rule for Redirection to Server for SSH on Port 27016

We've wrapped up the experiment that involved port forwarding, TCP rules, and connecting to an Ubuntu server using SSH from a client through a firewall, thanks to port forwarding.

#### Configuration of Ubuntu Server for openssh
Use the "nano" command to open the file located at /etc/ssh/sshd_config. Once opened, insert a new port for SSH; for instance, you can use 27017. This step is crucial to avoid port conflicts, especially when the firewall is accessible through SSH on port 22. After making this change, execute the command systemctl restart sshd to apply the modifications.

#### Configure firewall for port-forwarding 
The port forward setup specifies the destination as the OPT1 address, with the destination port set to 27016. Additionally, the redirect IP address is the static IP of the Ubuntu server in the Internal Network. The firewall automatically generates associated rules based on this configuration.

This is the associated rule generated by firewall

#### Proof of work 
The SSH connection is successfully initiated by a Debian client within the clientInternal network. It uses the IP address of the firewall's OPT1 interface and connects to the specified port 27016 as configured in the port forwarding settings. Importantly, secure shell access to the Ubuntu server is established through the TCP protocol without a direct client-server connection. Additionally, it's worth noting that the port on the Ubuntu server is set to 27017, not 27016. This highlights the effective functioning of both port forwarding and redirection.



## Conclusion
The carefully set up and tested firewall rules demonstrate how Opnsense provides detailed control over network traffic. We have specific rules for ICMP, TCP requests on ports 8081 and 8082, UDP requests on port 8083, and port forwarding for SSH on port 27016. These rules highlight the firewall's ability to manage various types of traffic.The Wireshark outputs provided solid evidence of the firewall's operations, illustrating successful or rejected communication attempts based on the defined rules. Notably, the SSH connection through port forwarding showcased the practical application of firewall configurations in enabling secure access to a server within a segmented network.
