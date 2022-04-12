# Covert Channels - Suspiciouser

**Scenario**:

We found some suspicious traffic on our network and think there could be some malware using covert channels to convey messages. Can you look at the isolated traffic and find it?

**Process**:

To start off with on this Pcap we look at the Protocol Hierarchy page:

![](<../.gitbook/assets/image (1).png>)

We can see that this is all TCP packets with a Data Field on some of the packets.

To start off let us dig into that data field and see what is going on here. We see the following characters in all the data fields: 5647686c49475a735957636761584d67626d39304946497a5a45677a4d334978626d6368

Let’s take this info to cyber chef and try to decode it. It looks like it was all in hex so here is the results of converting from Hex:

![](<../.gitbook/assets/image (19).png>)

Next we see that it looks like it could be in base64, so let’s decode that also:

![](<../.gitbook/assets/image (4).png>)

So, we know that this is not the answer, we need to look into the packets a little bit more to see what else we can find that is not right. In the packet information we see that the Urgent Pointer filed is highlighted:

![](<../.gitbook/assets/image (7).png>)

We can now see that with adding a column with the urgent pointer we see that all of the pointers are different. In the first packet we see that the hex is 5a 6d or Zm

The second packet is 78 68 or xh, Third Packet is 5a 33 or Z3 and the 4th packet is 74 55 or tU.

We have ZmxhZ3tU

This looks like a base64 pattern so let’s look at cyberchef and see if that decodes to anything that might resemble a flag.

![](<../.gitbook/assets/image (5).png>)

We have the start of our flag let’s pull the rest of the urgent pointers out to get the flag.

ZmxhZ3tUaGlzSXNTdXBlclVSR250R3V5c30K

This gives us our flag:

![](<../.gitbook/assets/image (20).png>)
