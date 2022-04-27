# Covert Channels - Suspicious

## **Scenario**:&#x20;

We found some suspicious traffic on our network and think there could be some malware using covert channels to convey messages. We isolated the suspicious traffic for you to take a look.

## **Process**:

To start off with on this pcap we look at the Protocol Hierarchy page

![](<../.gitbook/assets/image (6) (1) (1).png>)

We see there is only ICMP messages in this pcap, so the answer is in the 76 packets somewhere. And we have Request and Replies that seem to be echoing each other, so to start off we should look at one side of the conversation first. Also all the packets do not look too different from the ASCII section of the hexdump.

![](<../.gitbook/assets/image (3) (1).png>)

But after looking closer we are seeing information in the Data Section that looks “Suspicious”. But when we add the Data field as a column, we see a pattern in the data field, it looks like Hex.

![](<../.gitbook/assets/image (9) (1).png>)

When we pull out the hex from above (take out the 0 on all the data) we get this: 66 6c 61 67 7b 50 69 6e 67 48 61 73 44 61 74 61 3f 7d 0a

![](<../.gitbook/assets/image (2) (1).png>)

Put that in to a hex decode/CyberChef and we have decoded our flag.



### **Powershell Solution:**

```
-join ([string]::join("",(.\tshark.exe -r 'suspicious.pcapng' -Y "ip.src==192.168.17.7" -Tfields -e data | %{$_[1]})) -split '(..)'|? {$_}|%{[char][convert]::touint32($_,16)})
```

#### What is this doing?

tshark is reading in the pcap with a search filter of one of the two IP addresses.

It is then extracting out the single ping data field from the results

Then it is dropping the leading 0 from the results so that we can combine them to get the true hex result

joining the the data together into a single line

splitting all the data from one long list of characters to 2 char hex

converting the hex to ASCII

