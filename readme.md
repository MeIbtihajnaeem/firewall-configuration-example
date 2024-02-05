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

<img width="642" alt="image15" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/f2b1a54a-34f3-424a-9f19-7d8f97a7d904">

## Configuration of Virtual Box
I've established two distinct networks using the VirtualBox Network Manager. These networks are entirely segregated and are named InternalNetwork and ClientNetwork.

The ClientNetwork is defined with an IP address range from 192.168.2.1 as the Lower Bound to 192.168.2.254 as the Upper Bound. On the other hand, the InternalNetwork has an IP address range spanning from 192.168.56.1 (Lower Bound) to 192.168.56.254 (Upper Bound).

<img width="473" alt="image3" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/77d0f338-c647-4789-a1dc-aed8a8a5c961">


## Configuration of Firewall Interface & IP Address
Upon starting the Opnsense VM, the initial task post-login involves the assignment of interfaces and allocation of IP addresses. This process is crucial, especially considering the configuration of two adapters specified in the Opnsense network settings.

<img width="424" alt="image17" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/7ab666e2-f1a6-4f40-9d8b-9aac62deb965">

<img width="424" alt="image9" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/ed054821-0852-451e-b938-35a470fbba0a">


Both adapters are configured as Host-only networks. The first, designated as Network 1, serves as a private network connecting the Server and Opnsense. The others function as a public network accessible to clients. To manage this network segregation effectively, two interfaces are established on the firewall: the first is LAN, representing the private network, and the second is OPT1, serving as a distinct public network.

This configuration ensures that client access to the Server necessitates interaction with the firewall. After configuring these interfaces and assigning IP addresses, the Opnsense web GUI interface becomes accessible through the LAN interface. The visual representation of this setup is depicted in the image below.

<img width="366" alt="image18" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/7d0fba7a-7478-4d8c-88f8-471368eb393b">

<img width="726" alt="image19" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/d6b4db54-1bbb-42f9-b11f-58e8499abe4c">



## Configuration of Debian Client and Server
The Debian Client and Ubuntu Server are basic virtual machines running the Ubuntu and Debian operating system. The Client machine is equipped with Netcat, Web Browser and Wireshark to facilitate the demonstration of the firewall's functionality. On the other hand, the Server is configured to operate a straightforward SSH using OpenSSH on port 27016. This setup allows for a practical showcase of the firewall in action and the Server's functionality.


## Firewall Configuration and Rules
Below are all the mentioned firewall rules. I've configured them, and the following provides an explanation for each rule, one by one.

<img width="974" alt="image10" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/21b1464a-8759-4168-80dd-dfe22f4cdbfd">

### Rule for ICMP:
This rule states that all ICMP requests are permissible.
Terminal Result

<img width="453" alt="image4" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/967510eb-cdbe-499d-ae51-5de62215f001">

WireShark Output (Passed)

![image1](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/7b83079b-63a3-48b0-8795-ad51d4c29dea)


### Rule for TCP Requests:
This rule state that no TCP request on any port is allowed accept for port 8081 to 8082
Wireshark Output for port 8080 (Rejected)

![image11](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/545b7daa-c45c-4a3b-9623-c9eb025237ce)

There were unsuccessful attempts to establish a TCP connection from 192.168.2.1 to 192.168.2.2 on port 8080, resulting in connection resets (RST, ACK). 

Wireshark Output for port 8081 and 8082 (Passed)
The new request involves an initial TCP connection establishment from 192.168.2.1 to 192.168.2.2 on port 8081

![image20](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/1a9a1bf3-14ff-403c-9108-ea6197833c28)

![image14](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/fbf4a285-3a96-482d-9bb2-c4b593b69522)


### Rule for UDP Requests
Wireshark Output for port 8082 (Rejected)
The utilization of port 8082 for UDP requests promptly results in a "Destination unreachable" message indicating that port 8082 is not allowed to pass on UDP

![image7](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/85d6531f-256e-4543-b339-a53aa7e33ca1)

Wireshark Output for port 8083 (Passed)
The utilization of port 8082 for UDP requests promptly results in a "Destination unreachable" message indicating that port 8082 is not allowed to pass on UDP. However, when employing port 
8083, this error is not explicitly indicated. Nonetheless, no further output is received, as UDP does not acknowledge the request.

![image8](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/d637de43-a40a-4abd-8d31-ffd5322f8adb)

### Rule for Redirection to Server for SSH on Port 27016

We've wrapped up the experiment that involved port forwarding, TCP rules, and connecting to an Ubuntu server using SSH from a client through a firewall, thanks to port forwarding.

#### Configuration of Ubuntu Server for openssh
Use the "nano" command to open the file located at /etc/ssh/sshd_config. Once opened, insert a new port for SSH; for instance, you can use 27017. This step is crucial to avoid port conflicts, especially when the firewall is accessible through SSH on port 22. After making this change, execute the command systemctl restart sshd to apply the modifications.

#### Configure firewall for port-forwarding 

![image2](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/7674f0d0-370b-414c-aad9-cd722ba6f019)

The port forward setup specifies the destination as the OPT1 address, with the destination port set to 27016. Additionally, the redirect IP address is the static IP of the Ubuntu server in the Internal Network. The firewall automatically generates associated rules based on this configuration.

This is the associated rule generated by firewall

<img width="922" alt="image5" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/019f68c0-5323-4340-b00b-1bf118e087f0">

#### Proof of work 
The SSH connection is successfully initiated by a Debian client within the clientInternal network. It uses the IP address of the firewall's OPT1 interface and connects to the specified port 27016 as configured in the port forwarding settings. Importantly, secure shell access to the Ubuntu server is established through the TCP protocol without a direct client-server connection. Additionally, it's worth noting that the port on the Ubuntu server is set to 27017, not 27016. This highlights the effective functioning of both port forwarding and redirection.

<img width="373" alt="image13" src="https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/7a8827e7-8c43-4442-8eeb-010f91a2e9b0">

![image6](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/a095b2fb-dee1-4552-b537-a1ddc858c62f)

![image16](https://github.com/MeIbtihajnaeem/firewall-configuration-example/assets/55640263/70a9780a-562e-45af-bdf1-6d535189d139)



## Conclusion
The carefully set up and tested firewall rules demonstrate how Opnsense provides detailed control over network traffic. We have specific rules for ICMP, TCP requests on ports 8081 and 8082, UDP requests on port 8083, and port forwarding for SSH on port 27016. These rules highlight the firewall's ability to manage various types of traffic.The Wireshark outputs provided solid evidence of the firewall's operations, illustrating successful or rejected communication attempts based on the defined rules. Notably, the SSH connection through port forwarding showcased the practical application of firewall configurations in enabling secure access to a server within a segmented network.
