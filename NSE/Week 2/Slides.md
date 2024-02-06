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