#### Task desciption
Partial traffic packet captured from hacked machine, can you analyze the provided pcap file to extract the message from the packet perhaps by reading the packet data?

#### Solve
We are given pcap file as a task, so we should open it in wireshark
![[Pasted image 20241229041657.png]]
In protocols DNS and HTTP we can see queries to example.com.
In protocols TCP and UDP we can see queries we data: random TCP/UDP data.
So it is definetly not we are looking at, but there are a lot of ping requests, starting to analyze it, and we can see one byte in the end of the request that is an ascii symbol, in first four ping requests bytes are WGMY, and that is format of the flag, so we can get flag by checking all the ping requests: WGMY{1e3b71d57e466ab71b43c2641a4b34f4} 