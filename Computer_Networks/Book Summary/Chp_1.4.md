# 1.4 Delay, Loss, and Throughput in Packet-Switched Networks

Remember that Packet-switching is On-demand and Circuit-switching is Reservations.

This section is about examining and quantifying delay, loss and throughput in computer networks.

## 1.4.1 Overview of Delay in Packet-Switched Networks

A packet suffers from 4 types of delays at each node, e.g. router, along the path from one end to another end system.

### Processing Delay (also Nodal Processing)
The time required to examine the packet’s header and determine where to direct the packet is part of the processing delay. It can also inlude other factors, e.g. check for bit-level errors in the packet. Processing delays in high-speed routers are typically on the order of *microseconds or less*. 

### Queuing Delay
At the queue, the packet experiences a queuing delay as it waits to be transmitted onto the link. The length of the queue depends on earlier packets that are queued. If there are none queued, then the queuing delay will be zero. Queuing delays can be on the order of *microseconds to milliseconds* in practice.

### Transmission Delay
Assuming that packets are transmitted in a first-come-first-served manner, as is common in packet switched networks, our packet can be transmitted only after all the packets that have arrived before it have been transmitted. Let $L$ be the length of the Packet and $R$ the transmission rate of the link between to Routers in *bits/sec*. For example, a 10 Mbps Ethernet link has $R=10$ Mbps. The **transmission delay** is **$L/R$** and typically on the order of *microseconds to milliseconds in practice*.

### Propagation Delay 
The time required to propagate from the beginning of the link to router B is the propagation delay. The propagation speed depends on the physical medium of the link and is in the range of *$2\cdot 10^8$ m/s to $3\cdot 10^8$ m/s*, which is a little less than speed of light.

The propagation delay is the distance between two routers divided by the propagation speed.

Note : The transmission delay is the amount of time required for the router to push out the packet; it is a function of the packet’s length and the transmission rate of the link, but has nothing to do with the distance between the two routers. The propagation delay, on the other hand, is the time it takes a bit to propagate from one router to the next; it is a function of the distance between the two routers, but has nothing to do with the packet’s length or the transmission rate of the link

The total nodal delay is given by

$$
d_{nodal} = d_{proc} + d_{queue} + d_{trans} + d_{prop}
$$

Depending on different circumstances, different delays can be the dominant term of the nodal delay.

## 1.4.2 Queuing Delay and Packet Loss

### Queuing Delay

Unlike the other delays, the queuing delay can vary from packet to packet. Therefore, when characterizing queuing delay, one typically uses *statistical measures*, such as *average* queuing delay, *variance* of queuing delay, and the probability that the queuing delay exceeds some specified value.

Let $R$ be the transmission rate (*bits/sec*), $a$ be the average rate at which packets arrive at the queue (*packets/sec*) and all packet size $L$ (*bits*).

$$
\text{Traffic intensity : } \frac{L\cdot a}{R}
$$

- If La/R > 1  : queue increases overtime and queuing delay will grow exponentially
- If La/R <= 1 : Depending on the rate of packets arrival the delay can be zero for *periodical* arrival and increasingly larger for *bursty* arrivals.

Note that a small percentage increase in the intensity will result in a much larger percentage-wise increase in delay. Analogy to congested highways

### Packet Loss

As the queue capacity is finite in reality, packet delays do not really approach infinity as the traffic intensity approaches 1. Instead, a packet can arrive to find a full queue. With no place to store such a packet, a router will **drop that packet**; that is, the **packet will be lost**.

Performance at a node is often measured not only in terms of delay, but also in terms of the **probability of packet loss**.

## 1.4.3 End-to-End Delay

Assuming the network is not congested, we can omit the queuing delay. For $N-1$ routers between the end and source, the End-to-End delay is :
$$
d_{end-end} = N \cdot (d_{proc} + d_{trans} + d_{prop} )
$$

### Traceroute
Traceroute is a simple program that can run in any Internet host. When the user specifies a destination hostname, the program in the source host sends multiple, special packets toward that destination. As these packets work their way toward the destination, they pass through a series of routers. When a router receives one of these special packets, it sends back to the source a short message that contains the name and address of the router i.e. one can *trace* back the route the message took in the network.

You can visit [traceroute](http://www.traceroute.org/) and see for yourself.

### End System, Application and Other Delays
There are of course other types of delays. Here are a few examples that will be covered later in the course :
- Transmission delay in shared Mediums e.g. WiFi
- Media packetization delay : for VoIP applicaitons

## 1.4.4 Throughput in Computer Networks
To define throughput, consider transferring a large file from Host A to Host B across a computer network. This transfer might be, for example, a large video clip from one peer to another in a P2P file sharing system. The instantaneous throughput at any instant of time is the rate (in bits/sec) at which Host B is receiving the file. 

If the file consists of $F$ bits and the transfer takes $T$ seconds for Host B to receive all $F$ bits, then the **average throughput** of the file transfer is $F/T$ *bits/sec*.

- Internet Telephony : low delay and instantaneous throughput
- File transfers : delay not critical but higher throughput ideal
  
**Bottleneck Link** for two links with different rates $R_1$ and $R_2$ we want to have a throughput of $\min \{R_1, R_2\}$ for the path between the server and a client, as otherwise it will create a queue.

The throughput can simply be approximated as the minimum transmission rate along the path between source and destination.

The constraining factor for throughput in today's internet is typcially the access network.