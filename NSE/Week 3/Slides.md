## Spoofing
#### Internet layer identity
- At the Internet layer, a computer node is identified by its IP address
- Assigned by users or by software (DHCP)
- IPv4 and IPv6 addresses
![[Pasted image 20240206180120.png]]
![[Pasted image 20240206180134.png]]
#### Link Layer Identity
- At the link layer, a computer node is identified by the MAC (Media Access Control) address on their network interface card
- Primarily assigned by device manufacturers
- Also called hardware address, or physical address
- Example Ethernet MAC Address
![[Pasted image 20240206180151.png]]

#### How to translate between IP and MAC addresses?
- IP operates at the Internet layer and is not concerned with the MAC addresses of individual nodes to be used
- But an IP packet needs to be delivered in link layer frames on local area networks
- We need to know both the IP address and MAC address of a computer node
- Address Resolution Protocol (ARP) is therefore used to translate from IP address to MAC address
- Reverse ARP (RARP) to translate from MAC address to IP address
- Both operate below the Internet layer as a part of the interface between the Internet and data link layers
![[Pasted image 20240206180343.png]]
![[Pasted image 20240206180652.png]]![[Pasted image 20240206181146.png]]
#### ARP request message generation
1. ARP protocol is called by a hardware driver when an IP packet needs to be sent out on the local area network (LAN)
2. Get IP address of the target
3. Do I have the corresponding MAC address in my translation table?
	If yes, return the 48 bits MAC address to the hardware driver 
	If no, prepare the ARP request message	
	- Fill sender MAC address
	- Fill sender IP address
	- Fill target IP address
	- Target MAC address is filled with 0
4. The message is passed to data link layer where it is encapsulated in a frame
	- Source address: MAC address of the sender
	- Destination address: broadcast address, 12 Fs
	- The type is 0x0806
#### ARP request message reception
1. Every host or router on the LAN receives the frame
	- All nodes pass it to ARP
	- ARP Updates the translation table for the entry 
		<sender’s IP address, sender’s MAC address>
	- All nodes except the one targeted drop the packet
1. Target node replies with ARP message that contains its MAC address
	- Source address: MAC of the target
	- Destination address: MAC address of the sender
	- This is a unicast message
#### ARP reply message reception
1. Most nodes update their translation table for the entry
	<target’s IP address, target’s MAC address>
#### ARP translation table/ARP table/ARP cache
- To avoid having to send an ARP request packet each time by broadcasting, a host can cache the IP and the corresponding MAC address in its ARP table (ARP cache)
- Each entry in the ARP table is usually “aged” and contents are erased if no activity occurs within a period
- ARP table is updated when hearing an ARP request or ARP reply
- ARP is a stateless protocol, so most operating systems will update their table
- If a request is received, regardless whether they are the target
- If a reply is received, regardless whether they have sent out an actual request
#### ARP cache poisoning by spoofing
- Construct spoofed ARP replies and actively join the ARP protocol run
- A target computer A could be convinced to send frames destined for computer B to the attacker instead
- Computer A will have no idea that this redirection took place
- This process of updating a target computer’s ARP cache is referred to as "ARP poisoning/ARP spoofing/ARP poison routing/ARP cache poisoning"
![[Pasted image 20240206183324.png]]
#### MAC flooding with ARP spoofing

- Switches have an internal CAM (Content Addressable Memory) table, which maps switch ports to MAC addresses.
![[Pasted image 20240206183554.png]]
 - When a frame arrives at switches, this lookup table is checked to know which port to forward each specific packet out of where the receiver is located
 - In a MAC flooding attack, a switch is fed many Ethernet frames, each containing different spoofed source MAC addresses to consume the limited memory in the switch, especially old, lower-end switches.
- If the CAM table fills up and the switch cannot hold any more entries, some might divert to a fail open state.
- This forces significant quantities of incoming frames to be flooded out on all ports of the switch (as with a hub).
- This allows the attacker to then sniff traffic that might not otherwise be visible.
![[Pasted image 20240206183701.png]]

## DHCP
- Dynamic Host Configuration Protocol (DHCP) is a protocol that automatically provides clients with IP addresses and other related configuration (e.g., default gateway).
- Protocol run: DORA between client and DHCP server
	- Discovery: client broadcast	message to discover the server
	- Offer: server unicast message offering IP
	- Request: client accepting the IP	offered
	- Acknowledge: server acknowledges the IP and sends other related configuration
- DHCP request message contains the source MAC address of the network device requiring an IP address
- In a DHCP starvation attack, an attacker broadcasts large number of  DHCP_REQUEST messages with spoofed source MAC addresses
	- If the legitimate DHCP server in the network starts responding to all these bogus DHCP_REQUEST messages, available IP addresses in the DHCP server scope will be depleted within a very short span of time
	- Once the available number of IP addresses in the DHCP server is depleted, network attackers could set up a bogus DHCP server and respond to new DHCP DISCOVERY message from network DHCP clients
- DHCP spoofing attack
	- The rogue server starts distributing IP addresses and other TCP/IP configuration settings including default gateway and DNS server IP addresses, which can now point to an IP address controlled by the attacker
	- Facilitates man-in-the-middle attack and sniffing attacks

## Smurfing attack
- Broadcast a ping to your LAN
- Use ICMP Echo as the mechanism
- What happens? Denial of service (DoS)
#### Ping
- Ping is used to verify that a particular IP address exists and can accept requests
- Ping works much like a sonar echo-location, sending a small packet of information containing an ICMP ECHO_REQUEST to a specified computer, which then sends an ECHO_REPLY packet in return.
 ![[Pasted image 20240206184324.png]]
- Smurf attack via ping
	1. Smurf malware is used to generate a fake Echo request, containing a spoofed source IP, which is actually the target server address
	2. The request is sent to an intermediate IP broadcast network
	3. The request is transmitted to all of the network hosts on the network
	4. Each host sends a ping response (ICMP ECHO_REPLY) to the spoofed source address
	5. With enough ICMP ECHO_REPLY forwarded, the target server is brought down
	6. This causes denial of service (DoS)
![[Pasted image 20240206184543.png]]

- Amplification: small amount of traffic are converted into large amounts of traffic, in order to attack the target
![[Pasted image 20240206184651.png]]
#### NTP (Network Time Protocol)

- NTP: network time protocol allows computers to synchronise their clocks
- UDP based protocol, using port 123
- To keep consistent timekeeping among all clock-dependent devices within a network
- The time of a local system that runs NTP can be synchronised to other reference sources and used as a reference source to synchronise other clocks
- NTP uses a hierarchical, semi-layered system of time sources.
- Each level of this hierarchy is termed a stratum and is assigned a number starting with zero for the reference clock at the top.
- A server synchronised to a stratum n server runs at stratum n + 1.
![[Pasted image 20240206184951.png]]

#### How does NTP work?
1. The client sends the server an NTP message, which is timestamped when it leaves the client. $T_1= 09.00$
2. The NTP message arrives at the server. It is timestamped at the server too. $T_2 = 10.00.01$
3. When the NTP message leaves the server, the server timestamps it. $T_3 = 10.00.02$
4. The NTP message arrives back at the client. The local time is timestamped. $T_4= 09.00.03$

|**Timestamp** |**Value** |
| -------------- | ------ |
| $T_1$ | 09.00.00 |
| $T_2$ | 10.00.01 |
| $T_3$ | 10.00.02 |
| $T_4$ | 09.00.03 |
5. The client calculates:
	$D_1$ = $T_4 - T_1$ = total round trip delay = 3 seconds
	$D_2$ = $T_3 - T_2$ = processing time at the server = 1 second
	Round trip delay of NTP message on the network
	= $D_1-D_2$ = $(T_4-T_1) – (T_3-T_2)$ = 2 seconds
6. The client calculates the time difference between the NTP client and NTP server.
	$O_1$ = $T_2-T_1$ = 1 hour 1 second
	offset from the server’s clock to the client’s clock when the NTP message travels forwards
	from the client to the server
	$O_2$ = $T_3-T_4$ = 59 minutes and 59 seconds
	offset from the server’s clock to the client’s clock when the NTP message travels backwards
	from the server to the client
	Offset = $(O_1 + O_2)/2$ = 1 hour
7. Based on these parameters, the NTP client can synchronise its own clock to that of the server
#### NTP amplification attack
- Rely on the use of publicly accessible NTP servers to overwhelm a victim system with UDP traffic
- The NTP service supports a monitoring service that allows administrators to query the server for traffic counts of connected clients. This information is provided via the “monlist” command. This command returns a list of recent hosts that have connected to the service.
- Ntpd prior to 4.2.7 is vulnerable. Monlist command is enabled by default on older devices, and responds with the last 600 source IP addresses of requests which have been made to the NTP server.
- Use amplification between the NTP request and NTP response
	- Spoof the IP address to be the victim in a NTP request message
	- Send the NTP request message to vulnerable NTP servers, in which ask the server to run monlist command and respond
	- The monlist command causes a list of the last 600 IP addresses which connected to the NTP server, to be sent to the victim
#### NTP amplification attack process
1. The attacker uses a botnet to prepare NTP request packets with spoofed addresses to NTP servers, which have their monlist command enabled. The spoofed IP address is the victim’s IP address
2. The botnet sends the NTP request (a UDP packet) to NTP servers using its monlist command, resulting in a large response
3. The server responds to the spoofed address with the resulting data, normally of much larger size than the original NTP request, because monlist command returns the 600 hosts’ IP addresses.
4. The victim receives the response and the surrounding network infrastructure becomes overwhelmed with the amplification of traffic, resulting in a DoS attack.

#### How big is the amplification factor?
- NTP request = 234 bytes
- Monlist command can return 600 IP address maximally, that needs to be sent in about 100 packets as response, the Wireshark screenshot shows only 10 packets, each of 482 bytes long
- NTP response total = 100\*482 = 48200 bytes
- Amplification factor = 48200 / 234 = 206 (approximately)

## Botnets
- Robot and network!
- A botnet is a logical collection of Internet-connected devices (computers, smartphones or IoT devices) that have been infected and are controlled by a third party
- The attacker can control the botnet using a command and control (C&C) software
- The infected devices become “zombies” controlled by the owner (attacker) of the botnet
- Botnets are used for Distributed Denial of Service (DDoS) attacks and other types of attacks (spam, data theft, etc...)
#### How to build a botnet
1. A hacker purchases an exploit kit and uses it to start infecting users' computers. Inside the kit’s payload is a malicious application—the bot.
2. The bot instructs the infected PC to connect to a particular command-and- control (C&C) server. This allows the attacker (botmaster) to keep logs of how many bots are active and online.
3. The botmaster may then use the bots to gather keystrokes to steal online credentials and may rent out the botnet as a service for DDoS or spam emails, or sell the credentials online for a profit.
4. Depending on the quality and capability of the bots, the value is increased or decreased.

## TCP
- Transmission control protocol (TCP) is a transport layer protocol for many applications, such as HTTP for web, SMTP for email and ftp for file transfer.
- It is one of the main protocols in the Internet protocol suite.
- TCP provides reliable end-to-end connection-oriented service to applications, which defines a connection of ordered sequence of segments, over unordered IP network.
- Reliability is provided by segment sequencing, re-transmission and loss detection
- Also provide flow control and congestion control through the sliding window algorithm and loss detection of segments
-  How is a TCP connection identified?
	- By source IP address + port number, and destination IP address + port 	number
	- Port number links to applications running on hosts
- How is a TCP connection established?
	- By three-way handshaking
- Fields of the TCP header used in three-way handshake
	- Port numbers (16 bits each)
	- SYN flag (1 bit)
	- ACK flag (1 bit)
	- Sequence number (32 bits)
	- Acknowledgement number (32 bits)
![[Pasted image 20240206191401.png]]
- A SYN flood attack works by TCP client not responding to server with the expected ACK packet
- The malicious client can either not send the expected ACK, or more effectively spoof the source IP address in the SYN, causing the server to send the SYN-ACK to a falsified IP address – which will not send an ACK because it knows that it never sent a SYN.
- The server will wait for the missing ACK for a bit. However, the resources bound on the server may eventually exceed the resources available and the server cannot accept any legitimate clients
- Denial of service
#### SYN flooding process
1. The attacker, who is a botmaster here, instructs the bots to send a high volume of SYN packets to the targeted server, often with spoofed IP addresses
2. The server then responds to each one of SYN packets and leaves an open port ready to receive the response.
3. While the server waits for the final ACK packet, which never arrives, the attacker continues to send more SYN packets. The arrival of each new SYN packet causes the server to temporarily maintain a new open port connection for a certain length of time, and once all the available resources have been utilised the server is unable to function normally.
![[Pasted image 20240206191701.png]]
