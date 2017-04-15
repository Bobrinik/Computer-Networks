# TCP congestion control  [&6]
(21.03.2017)


## Outline
- Flow control vs Congestion control
- Reservation based vs Feedback based congestion control
- Router centric vs Host centric congestion control
	- Router states
- Per-flow congestion control feedback
- TCP congestion control
	- Host solutions
	- Additive increase/ Multiplicative decrease
	- Fast retransmit and Duplicate ACK
	- Fast retransmit and fast recovery
	- TCP start-up behavor
	- Slow start
	- Congestion avoidance
	- Decbit (destination experiencing congestion bit)
	- Router based congestion Avoidance
		- Random early detection
	- Source based congestion avoidance

## Summary

### Flow control vs congestion control
- Flow control gives a way to control how much packets the sender is sending. On the other hand, congestion control gives a way to control how much packets are circulating in a network as a whole. Congestion control gives a way to make sure that network is not overloaded and not under-utilsed.
- We need to keep in mind that demand for network resources can grow really fast and that every user in a network should be served fairly.
- Solutions:
	- We can preallocate resources to manage overloads
	- We can send data and then make our network drop data to decrease load
- Where can we implement this solutions?
	- Hosts or routers

## Congestion control
We can make our routers drop packets that are overloading network or we can make our routers put extra packets into a queue and serve them when load on a network decreases. Router should also be able to send feedback to source so that source can adjust its sending behavior. If source produces less packets, then network does not need to do any of the congestion control at all.

### Reservation based congestion control
Host may try to reserve a path to the destination by asking a network to allocate enough of resources so that its packets go to the source without any problem. Routers can allocate certain space or percentage for Host's packets. If such reservation is impossible, then flow is rejected.

### Feedback based congestion control
Host can be an asshole and just start sending data to the network. Network is going to send feedback to the Host. According to this feedback our host is going to adjust its rate.

## Router centric vs Host centric
- Router is a boss in deciding what can be forwarded and what is going to be dropped and if it is neccessary to tell host about anything.
- Host can be a nice girl and observe network conditions and adjust its behavior accordingly. It can observe network by looking at number of timeouts that occured, number of packets that were lost, or loss of wired connections.

## Router states
Even though we refere to certain packets as being connectionless, if we are sending certain set of packets from A to B. Most of these packets are going to travel through the same set of routers. They are going to flow through the same set of routers. This behavior emerges because of the topology of the network.

Note that router cannot see a channel but can see a flow. Channel is end-to-end concept since packets are flowing between two processes. 

Because a lot of packets are flowing, it makes sense to maintain certain state information for some flows. This information can be used to optimize certain flows. This router state is called <b>soft state</b>. Soft states don't need to be always created and removed by signalling. They can be setup in a network explicitly by sending a message to routers and routers would inspect packets source and destination pair or they can arise implicitly just because of networks topology.

By reading the above, you can think that there maybe per-flow congestion feedback that can help solve congestion of a network.
- It can be used but it is rarely done so. Network is already congested by sending more feedback we are going to make the situation worse.

## TCP congestion control

Idea behind TCP congestion control is to give each host a way to determine approximately how much capacity is available in the network.

- Methods of congestion control
	- Additive increase/ Multiplicative decrease
	- Slow start
	- Fast retransmit and fast recovery

- self clocking
	- we don't need external clock, we base it on feedback ACK
- additive increase/ multiplicative decrease
	- adjust to changes in available capacity

- congestionWindow: TCP state var simillar to AdvertisedWindow for flow control
- How do we know if network is congested?
	- Timeout is inefficient since we are waiting for a long time 
	- We still have timeout but we also use duplicated ACKs
		- If we have duplicate ACKs (ACks with same id), we have a congestions in a network so we need to reduce size of our window
- Fast retransmit ACKs
	- Problem: Coarse-grain TCP timeouts lead to idle periods
	- Solution: Fast retransmit: Use duplicate ACKs to trigger retransmission

## Congestion avoidance

- DEC bit (Destination Experiencing Congestion Bit) 
	- Router that experiences congestions sets bit to 1 in a packet
	- Other routers that do not experience congestion will forward this packet with the bit set. It kind off tells that there is some congestion on a route
	- Routers identify congestion and hosts are deciding what to do with this information
		- Destination puts congestion bit into ACK and sends it to source
			- If source get less then 50% of last window has experienced congestion, we increase CongestionWindow by One
			- If 50% and more, then we increase by 0.875
	- length of a queue is in EXAM 
- RED (Random Early Detction)
	- The basic idea is to drop packets fairly when we have a congestion happening
	- 
