---
description: Transfer
---

# Covert Channels - Transfer

**Scenario**:

We heard that someone was transferring something important over the net. We think it might be the flag to this challenge.

**Process**:

To start off with on this Pcap we look at the Protocol Hierarchy page:

![](<../.gitbook/assets/image (15).png>)

We see the following protocols:

UDP: DHCP, DropBox LAN SYNC, DNS, Data

TCP: TLS, HTTP, FTP, ICMP

For this pcap we see FTP in plain text, so we can start off looking at that protocol first.



![](<../.gitbook/assets/image (8) (1).png>)

We see that there was a file downloaded named zip.zip, but when you go to find the data it seems to be missing. We cannot export the FTP data files and the TCP stream does not give us the data of the transfer either.

![](<../.gitbook/assets/image (12).png>)

![](<../.gitbook/assets/image (1).png>)

The answer is to switch the filter from ftp to ftp-data. We now see the data that was transferred.

![](<../.gitbook/assets/image (10) (1).png>)

Follow the TCP stream to see all the data:

![](<../.gitbook/assets/image (14).png>)

Click on the Save as button to save the zip.zip file. We can see that the PK… is a Zip file and inside the zip file is a flag.png file.

![](<../.gitbook/assets/image (13) (1).png>)
