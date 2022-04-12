# Covert Channels - Needle in the Haystack

**Scenario**: We Captured a ton of traffic, can you sift through it to find anything interesting? Some people might think they are secure because they are hidden in the masses, the needle in the haystack.

**Process**:&#x20;

Start off with looking at the protocol hierarchy

![](<../.gitbook/assets/image (18).png>)

We can see that we have the following Protocols to look into:

\-          UDP: NTP, NBNS, LLMNR, DNS, Data

\-          TCP: TLS, Telnet, SSH, DRDA, Data, BitTorrent, Malformed Packet

We might want to look at the Malformed Packet as we are looking for a needle in the haystack, but that leads us to a dead end.

![](<../.gitbook/assets/image (16).png>)

Next thing to look at would be Telnet as this is easy to view because there is no encryption. We examine the Data field under Telnet to see what is being sent and we see all the characters being typed across the wire

![](<../.gitbook/assets/image (17).png>)

Here we get the answer in  plain text across the packets.
