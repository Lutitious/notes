## Hijacking
- Taking over what belongs to someone else, especially at runtime
#### How to hijack connections
- When is it appropriate for an adversary to use the hijacking technique?
	- To disrupt, or jam communications
	- To insert false or malicious data
- Similar to spoofing
- Difference is that, in general, hijacking takes over an existing connection, spoofing initiates new connection to cheat
#### Off-path vs on-path adversaries
- On-path adversaries are more powerful
- On-path adversaries can use connection state, but difficult to scale up
- Inserting false data is very difficult if off-path
![[Pasted image 20240206193958.png]]

#### TCP Connection
- How is a TCP connection identified?
	- By source IP address + port number, and destination IP address + port number
	- Port number links to applications running on hosts
- How is a TCP connection established?
	- By three-way handshaking
![[Pasted image 20240206194238.png]]

#### TCP three-way handshake
- Fields of the TCP header used in three-way handshake
	- Port numbers (16 bits each)
	- SYN flag (1 bit)
	- ACK flag (1 bit)
	- Sequence number (32 bits)
	- Acknowledgement number (32 bits)
![[Pasted image 20240206194549.png]]
#### TCP session hijacking
- When a connection is established between two hosts, the connection is supposed to be used only by these two hosts.
- If an attacker can inject their own data into this connection, the connection can essentially be hijacked by the attacker.
![[Pasted image 20240206194923.png]]
- The attacker needs to construct a valid TCP segment acceptable by the client/server
- Must get the following fields correct
	- Source IP address + port number (easy)
	- Destination IP address + port number (easy)
	- Sequence number (not so difficult if the attacker is on-path)

#### Predict TCP sequence number on-path
- OK if the attacker manages using $x+1$
- OK if the attacker uses $x+ \delta$ when the injected data is placed in the receiver’s buffer
- Discarded if $\delta$ is too big to allow the data in the buffer
![[Pasted image 20240206200050.png]]

#### Steps to on-path TCP connection hijacking
1. Sniff packets
2. Predict sequence number (client->server / server->client)
3. Inject data spoofing as client
![[Pasted image 20240206200144.png]]
#### What can the attacker do if off-path?
- Blindly spoofs as the client, and sends a SYN packet to the server as if it was from the client.
- The attacker must get the sequence number correct.
![[Pasted image 20240206200352.png]]
#### How to guess the initial sequence number
- Mismatch between specification and implementation
- RFC 793 (TCP specification)
- When new connections are created, an initial sequence number (ISN) generator is employed which selects a new 32 bit ISN. The generator is bound to a 32 bit clock whose low order bit is incremented roughly every 4 microseconds.
- Thus, the ISN cycles approximately every 4.55 hours. (4 microseconds x 2^32)
- Since we assume that segments will stay in the network no more than the Maximum Segment Lifetime (MSL, defined as 2 minutes in RFC 793) and that the MSL is less than 4.55 hours we can reasonably assume that ISN’s will be unique. \[i.e., sequence numbers should not repeat for at least 4.55 hours\]
- The purpose is to ensure undelivered packets from previous connection that arrive late do not overlap with current connection
- Unfortunately, BSD Unix TCP/IP stack did not adhere to these recommendations.
	- The sequence number for BSD TCP/IP stacks increases by 128,000 every second and by 64,000 for every new TCP connection.
	- Such a sequence is relatively easy to predict and can be much more readily exploited than one which follows the RFC standard
![[Pasted image 20240206200719.png]]
#### Example of TCP initial sequence number attack
- What the attacker does
	1. Swamp the Client C with ICMP flooding, taking it out of the picture (because the attacker wants to spoof as C)
	2. Create a real connection to port 80 on the web Server, and record the sequence number returned by the web Server.
	3. Close the connection with the Server.
	4. Create a raw IP socket, change its protocol to that of TCP, and change its source IP to that of the Client (by writing in the kernel)
	5. Send a SYN packet (supposedly from the Client) to port 80 on the web server.
	6. The server then sends an SYN+ACK to the Client C, which is silently ignored because C is under ICMP flooding attack
	7. Send an ACK packet to the server with the acknowledgement number equal to the sequence number previously recorded plus N+1
- After connection is established and hijacked
	- Send data to the Server, taking care to increment the sequence number each time by the amount of data sent. The Server thought the data comes from the Client.
## BGP: Border Gateway Protocol
- BGP is arguably the most important of all the Internet protocols
- Why? It glues thousands of ISPs in the Internet together
- BGP is an inter-Autonomous Systems (ASs) routing protocol
![[Pasted image 20240206201016.png]]
#### Autonomous system (AS)
- Autonomous System: On the Internet, an autonomous system (AS) is the unit of router policy, either a single network or a group of networks that is controlled by a common network administrator (or group of administrators) on behalf of a single administrative entity (such as a university, a business enterprise, or a business division).
- An autonomous system is also sometimes referred to as a routing domain.
- An autonomous system is assigned a globally unique number, sometimes called an Autonomous System Number (ASN).
![[Pasted image 20240206201206.png]]

#### Intra-AS routing
- For destinations that are within the same AS
	- RIP, OSPF
	- Routing table
- Remember the diagram from week 2?
	- Destination IP address is used to route packets
![[Pasted image 20240206202831.png]]

#### Inter-AS routing and CIDR prefix
- Inter-AS routing is also called inter-domain routing
- All ASs run the same inter-AS routing protocol, i.e., BGP
- In BGP, packets are not routed to a specific destination IP address, but to Classless Inter-Domain Routing (CIDR) prefixes, with each prefix representing a subnet or a collection of subnets
- Why is it called classless?
	- Class A: 8 bits for network part
	- Class B: 16 bits for network part
	- Class C: 24 bits for network part
	- Classless: any number of bits for network part

- CIDR prefix is the number of 1 bits in the subnet marks
- Example
	![[Pasted image 20240206203237.png]]
	- 172.16.122.204, the network part is 16 bits, and host part is also 16 bits
	- Subnet mask is composed of 16 ones followed by 16 zeros, 255.255.0.0
	- CIDR prefix = 16
	- The subnet can be expressed as 172.16.0.0/16 in CIDR prefix notation

#### BGP routing table
- Routing table is composed of entries
- Entry in the form of (x, I)
	- x is a CIDR prefix,
	- I is an interface number for one of the router’s interface
![[Pasted image 20240206203412.png]]

#### BGP functionalities
- Obtain network prefix reachability information from neighbouring ASs
	- BGP allows each subnet to advertise its existence to the rest of the Internet
	- BGP makes sure that all the routers in the Internet know about that subnet
- Determine the best routes to the network prefixes based on
	- Policy
	- Prefix reachability information
- ASs perform longest prefix matching to select which neighbours to route through
	- Example: when the address 192.168.20.19 needs to be looked up in a router having two entries in its routing table
		 ![[Pasted image 20240206203524.png]]
	- Both entries contain the looked up address, the longest prefix of the candidate routes is 192.168.20.16/28, making the route more specific, therefore is chosen
#### How is BGP route advertisement carried out?
- Routers exchange routing information over TCP connections using port 179
- eBGP, external BGP (TCP) connection spans two ASs
- iBGP, internal BGP (TCP) connection between routers in the same AS
![[Pasted image 20240206203741.png]]

#### BGP prefix hijacking
- When an AS announces route to network prefixes that it does not actually control
- Such false information is added to routing tables in BGP routers across the Internet
![[Pasted image 20240206203849.png]]
#### BGP sub-prefix hacking
- The attacker lies about a subset of the prefix rather than the whole prefix belonging to another AS
![[Pasted image 20240206203941.png]]
- The false route is chosen because BGP prefers longest prefix matching
#### How to create a routing blackhole?
- ASs advertises routes it cannot actually offer
	- Result: packets go into a network blackhole
- April 25, 1997: “The day the Internet died”
	- AS7007 (Florida Internet Exchange) de-aggregated the BGP route table
	- Learned the entire Internet routing table via BGP.
	- Converted most prefix to /24, making it more specific than those genuine routes
	- Re-advertised all prefixes as if AS7007 can route to them
	- Other routers on the Internet thought that every network everywhere could be reached by sending traffic to AS7007.
	- Huge network instability due to incorrect routing data
	- For full story of AS7007 incident from the network operator see: [here](http://lists.ucc.gu.uwa.edu.au/pipermail/lore/2006-August/000040.html)
- On February 24, 2008, Pakistan Telecom took down YouTube in an attempt to perform censorship (in Pakistan)
- Diverted YouTube traffic using a /24 prefix for YouTube subnets and leaked it in error
- For a concise yet interesting account of what happened, see: [here](http://web.mit.edu/6.02/www/s2012/handouts/youtube-pt.pdf)
![[Pasted image 20240206204348.png]]

## DNS
- What is DNS?
	- DNS stands for Domain Name System
	- A distributed databased implemented in a hierarchy of DNS servers
	- An application-layer protocol that allows hosts to query the distributed database.
- History of DNS
	- Humans find it easier to remember names like google.com than its IP address
	- Machines (routers) prefer fixed-length hierarchically structured IP addresses
	- We need a mapping!
	- Historically, mapping was maintained in a /etc/hosts file. This does not scale!
	- DNS is essentially a directory service that translates hostnames to IP addresses

#### Structure of DNS
- Tree like structure
- Root server (root name servers)
	- 13 root servers worldwide
- Top level domain (TLD)
	- com, edu, org, mil, net, gov. etc
	- uk, cn, de, eu .. etc
![[Pasted image 20240206204505.png]]
#### DNS name resolution
- It is the process used to map
- Names to IP addresses
- IP addresses to names
- A DNS Resolver performs lookups to do mapping, by contacting nameserver(s)
![[Pasted image 20240206204824.png]]

#### The need for DNS caching
- Both recursive and iterative lookups require many steps which are repeated many times across many lookups
- DNS performance is now critical for WWW
- Most links are human-friendly DNS names, not IP
- Need to cache DNS queries so that second and subsequent lookups are cheap and easy

#### Query QID attack
- Every DNS query has a QID
- DNS use connectionless UDP
- If the attacker responds to query with the right QID, then it wins, over the real nameserver’s response!
![[Pasted image 20240206205110.png]]

#### RRSet attack
- DNS response contains different Resource Record Sets, or RRSets.
- In particular, an “additional” section, where name server can give additional info that may be “useful” for future lookups.
![[Pasted image 20240206205255.png]]
- In an iterative query, the .com nameserver says you can ask ns.example.com  for the IP address of example.com. To help next request, an additional record might give IP for ns.example.com.
- Darth abuses this feature and adds an additional record that says the IP address of victim.com is 88.88.88.88
- FIX: Bailiwick checking (when asking for www.google.com, do not allow www.victim.com to be accepted as an additional record)
