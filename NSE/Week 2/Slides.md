## Networking
#### Network Protocols:
- Network conversation is carried out by nodes exchanging messages according to network protocols.
- A protocol is a set of rules or conventions that allow peer layers to communicate
- The key features of a protocol are:
	- Syntax: format of data block
	- Semantics: control information for coordination and error handling
	- Timing: speed matching and sequencing
#### TCP/IP:
- Application - Provides access to the TCP/IP environment for users and distributed information services. 
	- SMTP, POP3, IMAP (Email),
	- FTP (file transfer),
	- SSH (remote access),
	- HTTP (web), 
	- SIP (VoIP),
	- DNS (name service)
- Transport - Transfer of data between end points. May provide error control, flow control, congestion control
	- TCP (contention oriented),
	- UDP (connectionless)
- Internet - Shield higher layers from details of physical network configuration.  Provides routing and forwarding
	- CSMA/CD (Ethernet),
	- MACAW (WiFi),
	- PCF,
	- DCF,
	- 802.15.4 (IoT)
- Physical - Transmission of bit stream
	- Twisted pair, 
	- Optical fiber,
	- Satellite, 
	- Radio Modulation
![[Pasted image 20240206165805.png]]
![[Pasted image 20240206165845.png]]

## Sniffing
#### Passive Interception
- Two working modes for a network interface care (NIC)
	- Non-promiscuous mode: drops unintended traffic
	- Promiscuous mode: gets all packets from network
- Works for networks with broadcasting medium
	- Hubs
	- WiFi networks
- It does not work for networks using switches
	- A switch has separated broadcasting domain
![[Pasted image 20240206170130.png]]
![[Pasted image 20240206170147.png]]
#### Active Interception (port mirroring):
- Port mirroring is also known as SPAN (Switch Port ANalyser)
- Switch sends a copy of network packets seen on one port (or an entire VLAN) to the SPAN ports.
- Switch SPAN port mirrors all traffic through the switch
- Good purpose: network diagnostics, intrusion detection
- Malicious behaviour: if an attacker plugs into the port, they could see all traffic but it requires physical access to the switch
 ![[Pasted image 20240206170421.png]]

#### Active Interception (network tapping):
- A hardware device inserted at a specific point in a network where data can be accessed for testing or troubleshooting purposes
- TAP = Traffic Access Point or Test Access Point is a layer 1 device
![[Pasted image 20240206170715.png]]
- Pin Tapping is where you tap into the pins of the ethernet port directly
	- Pins 3 and 6 of TAP A are connected to pins 1 and 2 of Host A and Host B
	- Pins 3 and 6 of TAP B are connected to pins 3 and 6 of Host A and Host B
	- Cause brief disruption as cable needs to be separated
![[Pasted image 20240206170905.png]]
- Bypass TAPs Put inline on the critical links
- Every packet being sent from the network will get to the monitoring tool
- Can also work in the Bypass mode to bypass the monitor appliance and keep the critical link running
![[Pasted image 20240206171118.png]]
- Vampire taps Pierce the shielding of copper wires in order to provide access to the signal within.
- This may bring down the link
- Used a lot by Tel-co engineers
 ![[Pasted image 20240206171307.png]]

#### Rogue Access Points
- A rogue access point is a wireless access point that has been installed on a network without explicit authorisation from a local network administrator
- Types:
	- Hardware: a wireless router
	- Software: a computer/laptop with virtual one
- They are usually open and try to mimic a known WiFi network ID, for example eduroam.
- Victims are not aware that they are connecting to a rogue AP.
![[Pasted image 20240206171518.png]]

## Traffic Analysis

#### Data Acquisition
- We have chunks of bytes from the physical layer after sniffing
- Data acquisition is the process of understanding the meaning of the captured traffic
- Who is talking to whom about what?
- Network conversations are carried out through network protocols
- That is: we need to understand the messages exchanged by protocols
#### Libpcap
- Libpcap is an API for capturing live network traffic
	- Written in C
	- libpcap, developed by the tcpdump team, for Linux and Linux like OS
	- Npcap, for windows (Winpcap is unmaintained since 2013)
- Although it is called packet capture, it actually captures frames, which include
all data from data link layer to the application layer, due to the nature of
encapsulation
- Network sniffers or analysers capture network traffic and save them in .pcap
files
- Network sniffers or analyser are able to open .pcap files created by other tools
- Network traffic dataset can be pcap files

- The core of Libpcap is BPF, Berkeley packet filter
- BPF was proposed by Steve McCanne and Van Jacobson in 1992
- To solve the problem of inefficiency in packet capturing at the time
- Problem:
	- Packet filter module sits on top of packet capture module
	- Each packet received is copied always, to a buffer, which is then sent upstream to the packet filter, which may decide to discard the packet.
	- Many CPU cycles will be wasted copying unwanted packets
- BPF solution:
	- Filter the packet before buffering the required parts of it
	- The filter is defined by users (which are network sniffers here)
	- 100 times faster than the existing packet capture tool at the time
- A kernel agent that discards unwanted packets as early as possible
- Two components:
	- Network tap
	- Packet filter
- BPF feeds the packet to each participating process’ filter. This user-defined filter decides whether a packet is to be accepted and how many bytes of each packet should be saved.
- For each filter that accepts the packet, BPF copies the requested amount of data to the buffer associated with that filter.
![[Pasted image 20240206172600.png]]
- Use a directed acyclic control flow graph (CFG)
- The example shows shows a CFG filter function that accepts all packets with an Internet address foo
- Right branch if yes to the condition in the oval
- Left branch if no to the condition in the oval
![[Pasted image 20240206172855.png]]

#### Types of traffic analysis
- Protocol analysis: analyse individual protocol inside a packet
- Packet analysis: analyse protocols of different layers inside a set of packets
- Flow analysis: analyse a flow composed of multiple packets
![[Pasted image 20240206173209.png]]

- Why do we need protocol analysis?
	- To understand the semantics of the information being transmitted
	- To interpret the information
- The specification of many protocols is public
- IETF Request for Comment (RFC) documents
	- RFC 791 – IP v4
	- RFC 793 – TCP v4
	- RCC 2613 –HTTP v.1
	- List of RFCs https://en.wikipedia.org/wiki/List_of_RFCs
- Support for public protocols is usually implemented in tools like Wireshark
	- Wireshark shows headers, flags, content in a more user-friendly and navigable way

- Why do we need packet analysis?
	- To inspect the protocols within a set of packets transmitted
	- To identify packets of interest using packet analysis techniques
- What does packet analysis do?
	- If you are network professionals
		- Monitor the health of a network
	- If you are security professionals
		- Passive network vulnerability assessment
	- If you are an attacker
		- Passive attack tool
		- Steal information such as passwords
- The word packet is misleading
- Frames are actually captured and analysed
- Frames are link layer PDU (processing data unit), Internet layer packet is the payload of frames
![[Pasted image 20240206174021.png]]
- Pattern matching
	- Identify packets of interest by matching specific values in the packet capture
	- E.g., if source IP address = foo
- Parsing protocol fields
	- Extract the contents of particular protocol fields
	- E.g., Wireshark shows the contents of each field in an IP packet
- Packet filtering
	- Separate packets based on the values of fields in protocol metadata
	- E.g., showing ICMP packet only

- Why do we need flow analysis?
	- Identify patterns (e.g., repeated communications)
	- Isolate suspicious activity and discard irrelevant data`
	- Analyse higher-layer protocols (e.g., reconstructing TCP segments and 	get the full picture of the protocol encapsulation in it: HTTP, SSL …)
	- Extract data (e.g., a binary file to be analysed)
- What does flow analysis do?
	- Flow analysis is the practice of examining related groups of packets.
- Use Wireshark “Follow TCP Stream” feature
	- Select any packet inside a TCP stream
	- Choose menu Analyse->Follow->TCP Stream
	- Wireshark reconstructs the full duplex contents of that stream from beginning to end
	- Conversations, transactions and file transfers that span multiple packets in a stream can be reconstructed in their entirety
	- Include only info that is contained within the packet capture
- Use Wireshark “Follow HTTP/UDP/ Stream” feature

- List conversations and flows
	- List all conversations and/or flows within a packet capture or only specific flows based on their characteristics
- Export a flow
	- Isolate a flow or multiple flows, and store the flow(s) of interest to disk for further analysis
- File and data carving
	- Extract files or other data of interest from the reassembled flow
