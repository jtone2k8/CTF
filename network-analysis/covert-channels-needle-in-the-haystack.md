# Covert Channels - Needle in the Haystack

**Scenario**: We Captured a ton of traffic, can you sift through it to find anything interesting? Some people might think they are secure because they are hidden in the masses, the needle in the haystack.

**Process**:&#x20;

Start off with looking at the protocol hierarchy

![](<../.gitbook/assets/image (18) (1).png>)

We can see that we have the following Protocols to look into:

\-          UDP: NTP, NBNS, LLMNR, DNS, Data

\-          TCP: TLS, Telnet, SSH, DRDA, Data, BitTorrent, Malformed Packet

We might want to look at the Malformed Packet as we are looking for a needle in the haystack, but that leads us to a dead end.

![](<../.gitbook/assets/image (16) (1).png>)

Next thing to look at would be Telnet as this is easy to view because there is no encryption. We examine the Data field under Telnet to see what is being sent and we see all the characters being typed across the wire

![](<../.gitbook/assets/image (17) (1).png>)

Here we get the answer in  plain text across the packets.

#### PowerShell Solution:

```
-join (.\tshark.exe -r 'needleinahaystack.pcapng' -Y "telnet" -T fields -e telnet.data | ?{$_ -cne " "})
```

join at the beginning will combine all the items into a single line which will make it easier to read (after the follow is ran)

Tshark –r will read in the pcap

\-Y is the filter of telnet

\-T fields is to format the output as text based of fields of the packet

\-e is the field name to extract; here it is the telnet.data

All piped to remove any blank lines
