# Inter-networking

## Short summary
- We are trying to build host-to-host packet delivery service

## Outline
-  Message transimission
-  Host addressing and address translation
-  Fragmentation and reassembly
-  Error reporting / control messages
-  Dynamic configuration
- IPv4 address exhaustion
	- DHCP-like protocols
	- NAT (network address translation)
	- Subnetting (VLSM)
	- Supernetting (CIDR)
	- IPv6

<hr>

## Message transmission
When we transmit messages, we need to include destination of messages. For example in datagram connection, our packet has a destination header and each switch uses its forwarding table to decide on which port to pass this packet.

## Host addressing
We need some reliable way to make addresses for our network. So that address translation by switches is done efficiently.
- IPv4
- IPv6

## Fragmentation and reassembly
Because we are dealing with interconnected networks, it is possible that different networks have different formats for frames. However, when we are sending our packet from source, we cannot know this. Therefore, we need an algorithm or some procedure that is going to divide our huge packet into smaller packets and send it over the network. 

## Error reporting / control messages

Because our huge packet is going to be divided into smaller packets we will have to do CRC, two dimensional parity, or IP checksum on each of the packets.
- CRC (Cyclic Redundancy Check)
- IP cheksum
- Two dimensional parity

Exercise: Error reporting
> Suppose all the bits in the packet including checksum are overwritten. Could a packet with all 0s or 1s be a legal IPv4 packet? Will the Internet checksum catch that error why or why not? 

## Dynamic Host Configuration Protocol

Our networks are going to become huge and it will be impossible to reconfigure each node in it manually. Therefore, we need our network to be self-configuring. We have DHCP server that is going to assign IP adresses to newly connected nodes on the network.  
- Dynamic Host Configuration Protocol

<hr>


## Network-level protocol ( IP ) or Internet Protocol

Internet Protocol: IP runs on all nodes in the network and defines infrastructure that permits these nodes and networks to function as a single logical network.

Physical layer -> Data link layer -> Network layer

Network-level protocol :

1. It operates on all hosts and routers 
	* Router: connects networks to the internet 
	* Router has different IP addresses for each connected network
	* -192.168.0.1-[ROUTER]-192.168.1.1-
	* Router has a table inside which  maps its MAC addresses to the IP addresses inside of the network. Router itself has a MAC address. In other wods, one router with 4 ports has 5 MAC addresses.

## Message transmission IP datagram

- IP datagram contains the IP address of the host
- The network part of IP identifies a single physical network.
- If node is not connected to the same physical network, then it needs to send the datagram to a router
- Node can have couple of routers among which it has two choose the best (least amout of hops)

```
if(destination in myInterfaces):
	myInterfaces.sendTo(destination, DATA)
else:
	if(destination in forwardingTable):
		forwardingTable.sendNextHop(dest,data)
	else:
		defaultRouter.send(dest, data)
```

## Fragmentaion and reassembly
- Every network has its own Maximum Transfer Unit because different networks have different hardware
- Fragmentation happens in router
	- All packets resulting from fragmentation of bigger packet carry an identifier in the Ident field to permit reassembly
	- Ident is chosen by host and is unique among all datagrams
- We do reassembling at the destination (currently)

Exercise:
> The designers of IP decided that fragmentation should always happen on 8-byte boundaries, which means that the Offset field counts 8-byte chunks, not bytes. (Figure out why this design decision was made.)

## Host Addressing
We need to have some method or  a way to identify hosts on the network.

- IP addresses are hierarchical and have two parts: 
		- Network part
		- Host part 
	- The sizes of network part and host part vary 

* All hosts attached to the same network have the same network part
* Routers are attached to two networks so we need to have different address for both sides
* Usually we divide IP addresses into three classes. Each class defines different size for network part and host part.
	* It means that the number of hosts and networks is different for different classes
* The class of IP is identified by the most significant few bits

Most significant bit: it is the bit wich has the greatest value.

Idea behind addressing:
- Class A - WAN
- Class B - Campus size
- Class C - LAN

Classes review:

|Class|Network|Host|
|---|---|---|
|0|7|24|
|10|14|16|
|110|21|8|

## IPv4 address exhaustion

|Class|Number of Networks|  Number of Hosts|
|---|---|---|
|A|126|24|
|B|16384|65534|
|C|2097152|254|

- Reserved addresses:
	- 0.0.0.0 reserved fore refering to itself
	- 255.255.255.255 reserved for broadcast

### Number of possible hosts:

* 2^8 = 256 (A)
* 2^16 = 65536  (B)
* 2^24 = 16777216 (C)

- Imagine that we have a company that has 100 hosts. Address class should it use?
	- If we have one host in class B, then we waste 254 addresses. Soring huge forwarding tables is very expensive. More network numbers means bigger forwarding tables.
	- The problem is that we are running out of network numbers. Therefore, we decided to use same network numbers for two different physical networks. This is the idea of subnetting.


### Subnetting

> Subnet mask is like masking when painting. You place a mask over what you do not want to paint on. 

- Why do we use subnet mask?
- What is a subnet number?
- How do we calculate subnet number?
- How do we use subnet number in routers?

### Why do we use subnet mask?
- The subnet mask enables us to introduce a subnet number. All hosts on the same <b>logical network</b> will have the same subnet number, hosts may be on <b>different physical networks</b> but share a single network number.
- Subnet mask establishes the number of possible IP addresses used on a network
- It allocates a single IP address range to several physical networks

```
1 0 0 0 0 0 0 0 1 0 1 0 1 1 1 0 1 0 0 0 1 1 1 0 1 1 0 0 1 0 0 0 <- Host
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 0 0 0 0 0 0 0 0 <- Subnet Mask
                                                0 0 0 0 0 0 0 0 <- allowed hosts

8 last bits is a number of allowed hosts on a network because they are
not included into a subnet number

Therefore we can have on this subnet work:
2^8 hosts
```

###  What is a subnet number ?
-  It is a number of a subnetwork. It gives as a way to tell if some HOST is on our subnetwork or not. For example, we want to send a packet to some host. If this host is on our network then we can send it to him directly. Otherwise, we need to send the packet to the router.
- Basically, subnet number allows us to particion IP addresses per subnetwork.

### Why do we need subnet number?
- It gives us a possibility to particion IP address space.

### How do we calculate subnet number?
- Subnet Mask AND Host IP -> subnet number
	- 128.96.34.15 AND 255.255.255.128 --> 128.96.34.0

```
Subnetting example:
[ 1 | 0 | Network 14 bits | Subnet 8 bits | Host 8 bits ]
```

### How routers are using subnet number?

Router holds the following table:
| SubnetNumber | SubnetMask | NextHop |

- Router then does AND between incoming IP packet and SubnetMask to find where to route the packet
- In case we cannot find the right match we send the packet to the default router




### Ideas
- Aggregation of routing information 
	- Networks outside of the subnet see only one network; however, subnets inside of this network have to deal with routing to neighboring subnets.

### Exercises
- If we have CLASS B address and want to support 2^6 subnetworks, how our subnet would look like?
-  What is the maximum number of IP addresses that can be assigned to hosts on a local subnet that uses the 255.255.255.224 subnet mask?
	- 224 is  1 1 1 0 0 0 0 0 therefore we can have 2^5 hosts which is 32 -2  => 30 hosts
		-  0.0.0.0 is used for desigining self
		- 255.255.255.255 is used to broadcast
	-  2^7 since we are substracting one since at initial position it is 2^0

## Supernetting or Classless Interdomain Routing
- Combine multiple Class C addresses for a larger network
- Use contiguous blocks of Class C addresses
```
Suppose we assign 192.4.16 - 192.4.31 network numbers
Top 20 bits in these addresses are the same
1100000000000100 0001
```

- We have 20bit network number
- It is something between Class B and Class C in terms of number of hosts we can support
- In order for this to work we need to handout blocks of class C addresses that share a common prefix, this means that each block must contain a number of class C networks that is a power of 2. //because otherwise there will be different prefixes
- 192.4.16/24 <- 24 is a length of prefix (contiguous mask from the most significant bit)


## IPv6 addresses
- 128 bits
- String og eight 16 bit values separated by colons

```
 5CFA:0002:0000:0000:CF07:1234:5678:FFCD
We can compress:
5CFA:0002::CF07:1234:5678:FFCD
- note that we can compress only once
```
## DHCP
- Why do we use DHCP?
- How does DHCP work?
- What is DHCP relay?

Dynamic Host Configuration protocol automatically assigns IP addresses and other network configuration information (subnetmask, broadcast address, etc) to computers on a network.

1. When host connects to network it sends request to 255.255.255.255 
2. DHCP server hears this and sends back an IP address to the requester
3. DHCP server makes a record in its table where it assigns new IP address for the requester

- In larger organizations
	- We build a relay  from DHCP relay to DHCP server
	- It means that when new host is connected to a network then it asks for IP its local DHCP server (it might be on router)
	- Local DHCP server communicates to higher level DHCP through DHCP relay
	- Higher level DHCP assigns IP to the host connected?

# Vocabulary
- UDP (User Datagram Protocol)
- TCP (Transmission Control Protocol)
- MTU (Maximum Transfer Unit) is the larges IP datagram that network can carry in a frame
- Forwarding is a process of taking a packet from an input and sending it out on the appropriate output
- Routing is the process of building up the tables that allow the correct output for a packet to be determined
- Routing determin output
- CIDR (Classless interdomain adressing)
- ARP (address resolution protocol)
- DHCP: Dynamic Host Configuration Protocol
- Most significant bit: it is the bit wich has the greatest value.

## Bridges, Switches, Routers
- Bridges are link-level nodes 
+ They forward frames from one link to another to implement an extended LAN
- Switches are network level nodes that forward packets from one link to another to implement a packet-switched network
- 

# Extra stuff from internet
- ip_address/<number> 
- <number> indicates a bit length of a subnet mask
- Subnet mask is like masking when painitng
	- you place a mask over what you do not want to paint on
 
