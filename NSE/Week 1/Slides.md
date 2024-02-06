## Cyber Security

• "Cyber security is how individuals and organisations reduce the risk of cyber attack”
• Network security is how individuals and organisations reduce the risk of network attack.
![[Pasted image 20240131155015.png]]

## CIA Triad
#### Confidentiality
• Assures that private or confidential information is not disclosed to unauthorised individuals
• For example, a university ensures that no other students can see your grades except yourself on the system
• Loss of confidentiality = unauthorised disclosure of information

#### Integrity
• Assures that information and programs are changed only in a specified and authorised manner.
• E.g., your timetable can only be changed by the faculty timetabling team when authorised by the programme team
• E.g., using CRC (Cyclic Redundancy Check) at data link layer in digital communication to detect (and correct) accidental changes to raw data (0->1, or 1->0)
• Loss of integrity = the unauthorised modification or destruction of information

#### Availability
• Assures that systems work promptly and service is not denied to authorised users.
• E.g., Keats load module contents
• Loss of availability = the destruction of access to information (system) or the destruction of use of information (system) 

![[Pasted image 20240131155414.png]]
## Six Points of Security
#### Authentication
• Assures that an entity is the one they claim to be
• You identify yourself to Keats as a genuine student at King’s College London
• Failure of authentication is spoofing: a person or program successfully identifies as another by for example guessing the password.
• Fundamental to other security properties

#### Non-repudiation: 
• Assures that neither sender nor receiver can deny transmitted message or transaction.
• E.g., when an email is sent, the receiver can prove that the alleged sender sent the email. When an email is received, the sender can prove that the alleged receiver has received the message.
• E.g., Important security property in e-commerce

#### Code validation:
• Assures that software/firmware codes are sound and checked for vulnerabilities
• E.g., IoT devices
• Security engineering

![[Pasted image 20240131160213.png]]


## 5Ds of Perimeter Security
#### Deter:
•  dissuade the potential attackers before they try to compromise the security service
• make the attack too difficult (using a 1024-bit prime in ElGamal encryption) or not worthwhile to attack

#### Detect:
• monitor for attacks
• Intrusion detection software
• ”Sensors” at edge computing
• Traffic analysis and machine learning to detect abnormalities

#### Deny:
• prevent unauthorised access
• Firewalls, access control
• Authentication, authorisation, freshness

#### Delay: 
• slow down the attacker
• Maximise the time between the detection of an attack and the attack
reaching its target
• Put more bits for encryption key

#### Defend: 
• fight with the attacker
• More suitable for physical security
• Drop malicious packets as close to the source as possible

## Security Mechanisms
• ITU (International Telecommunication Union) X.800
• Approved in 1991!
• https://www.itu.int/rec/T-REC-X.800-199103-I
• Specific security mechanisms
	• May be incorporated into the appropriate protocol layer
	• Provide some of the OSI security service: authentication, access control, data confidentiality, data integrity, and non-repudiation
	• Very much related to popular security properties
• Non-specific security mechanisms
	• Not specific to any particular OSI security service or protocol layer

#### Jamming:
• Jamming takes up the transmission channel regardless of the rules specified by network protocols and affects availability for legitimate packets
• Jamming is an effective attack at link layer when the transmission media is broadcast-based, e.g., Ethernet, air/wireless
• Media access control protocols
	• Static (e.g, TDMA, FDMA, CDMA) or contention based (Aloha, CSMA/CD, MACAW)
	• Users must follow the protocol to share the media
	• Backoff when the channel is busy or after a collision
	• But a jammer does not want to share the media, but to make it unavailable to others.

• Could be applied for benign purpose
• Can be used at meeting rooms, conference
rooms, banks, and more places that require
silence, confidentiality and information security
![[Pasted image 20240206163710.png]]

#### Sniffing:
• Listening to the network conversations that are not intended for you
• Network interface card (NIC) can be set to promiscuous mode to get all packets from network
• Packet sniffers intercept and log network traffic that they can see via the NIC
• Eavesdrop on unencrypted data in the packets
• Capture passwords and authentication tokens if they are sent in the clear
• Capture packets for later playback in replay, man-in-the-middle, and packet injection attacks
• Packet sniffer can have a benign purpose: useful for network debugging and diagnostics.
![[Pasted image 20240206163915.png]]

#### Spoofing
• Masquerading/impersonation – pretend to be somebody you are not
• What identifies somebody in computer network?
	• Application layer: DNS address, e.g., an URL, an email address
	• Network layer: IP address
	• Link layer: MAC address
• Spoofing forms the basis of a lot of attacks
	• Why? Because it is very easy to change your address
• Email spoofing: typically associated with
a sense of urgency
• Text message spoofing: smishing, often contains a malicious link to
acquire your personal information
• Call ID spoofing: often target at the the elderly
• URL spoofing: fake domain and website
• DNS spoofing: cache poisoning
• IP address spoofing: change the source address in an IP packet
• MAC spoofing: changing a MAC address of a NIC