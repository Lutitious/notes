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