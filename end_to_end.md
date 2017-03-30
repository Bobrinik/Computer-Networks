# End-To-End protocols [&5]

## Summary
- What is End-to-End?
- How does UDP work?
	- What is a port?
	- How does server learn about client's port?
- How does TCP work?
	- How is TCP's sliding window different from direct-link sliding window?
- What is a difference between TCP and UDP?
- What does End-to-End argument tell us?
- What does SYN stand for?

## What did we see before?
1. Physical layer is responsible for delivering data from Node A to Node B where both nodes are directly connected
2. Datalink layer is responsible to make sure that data is delivered reliably
3. Network layer makes sure that there is a connection beetween Node A and Node C where Node C is located on a different network
	- IP
4. Transport layer is responsible for making connection between Node A and Node C reliable
	- UDP and TCP


## What are we going to learn here?
Here we want to turn host-to-host packet delivery service into a process-to-process communication channel. This is the role played by the transport level of the network architecture, which, because it supports <b>communication between application programs running in end nodes</b>, is sometimes called the end-to-end protocol.

## What do we expect Transport protocol to provide?
- Gurantees of message delivery
- Delivery of message in the same order it is sent
- Delivery of at most one copy of each message
- Support of transmission of  arbitrary large messages
- Supports synchronization between the sender and receiver
- Allows the receiver to apply flow control on sender
	- Receiver can tell sender to send less or more packets
- Supports multiple application processes on each host


## UDP
It just extends host-to-host delivery to process-to-process delivery. It adds an extra level to give a  way to identify a process on a host.

There are a lot of different process running on the host so we need a way for our protocol to choose to which service the packet should be delivered. We could do it by identifying process by its corrresponding (PID). We usually do this in cases when we have a closed distributed system and every PC has the same OS running. In most cases, we identify each process by an abstract locator called PORT.

- You send to port and process receives through this port
- UDP keeps port of the process that sent packet and keeps port of the process to which the packet is sent to
	- <port, host> pair uniquely identifies process
- UDP uses checksum to ensure correctness

### How does a server and client learn about each others' ports?
- Server learns about clients port when client connects to it
- How does a client know about server's port?
	- We agree that certain services will always run on specific ports
		- Sometimes we agree by using the well known port in the beginning, but after we decide to use another port
	- We could use a port-mapper. This service will basically catch all packets and will send them to their appropriate ports
- To demultiplex incomming connections we use ports
		- DNS 43
		- SSH 22
		- Telnet 23




## TCP
It offers reliable,  connection-oriented, byte stream service. It frees applications from caring about missing and reordered data.

- Full duplex protocol (bidirectional flow of data)
- Receiver can limit how much data the sender can be sending at any given moment (flow control)
	- Keep sender from overloading receiver
- It can demultiplex to processes
- It implements congestion control
	- Keeping sender from overloading a network

### What is a difference between sliding window and TCP sliding window?
1. TCP supports connection between two running processes
2. RTT for physical link sliding windows is the same for TCP it varies
3.  Packets can be reordered as they cross the internet
4. Sliding window for physical links is designed for each type of host differently. TCP sliding window should work on any host
5. Network congestions might arise

- End-to-End Argument:
	- a function should not be provided at the lvel if it cannot be completely implemented at that level
	- For this reason, we cannot implement congestion control and ordering at the IP level. Because they cannot be implemented completely at IP level

## TCP usage

- Sender fills up packet with bytes and then sends it to some peer over the network
- Receiver takes bytes from the network and puts bytes into a buffer. 
- Packets exchanged between peers in TCP connection are called segments because each packet is a segment of a byte stream

### Connection establishment  and termination

- Three-way handshake

```
Client sends a segment to the server stating the initial sequence number it plans to use

The server then responds with a single segment that both acknowledges the client’s sequence number (Flags = ACK, Ack = x + 1) and states its own beginning sequence number (Flags = SYN, SequenceNum = y)

The client sends ACK of server's sequence number
```
### Why client and server cannot start from the same sequence number?
- Each side of TCP connection should choose starting sequence number at random
- We do it to protect ourselves from two incarnations of the same connection reusing the same sequence number too soon
	- There is a chance that segments from earlier incarnations may arrive during receiving of segments of later incarnations
	- Incarnation: two hosts can establish and reestablish connections. Consequtive establishments and restablishments are called incarnations

## TCP sliding window
- How is receiver calculating the size of advertised window size?
- Why do we use scaling factor for advertised window?
- How do we protect agains wraparound of a sequence number?
	- How can we calculate the rate at which a sequence number is exhausted?

## Summary
- End-to-end describes a way to connect two processes running on two hosts. It is a network layer responsability
- UDP and TCP is used to ensure connection between two processess running on different hosts
	- To identify processes on host we use ports. Port is just a logical construct that gives a way to reference a process
- UDP only provides a way for packet to target specific process on a host
	- Packets can be lost, Packets can arrive out of order
- TCP has UDP's process targeting and it has implements extras to have reliable connection, let packets arrive in order
	- Three way handshake
	- It is done by using sliding window algorithm
		- In this modification receiver can tell sender to send less

## Interesting stuff
- Port knocking: You try to access certain ports in some sequence, at the end certain port i going to be opened
