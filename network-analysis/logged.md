# Logged

## Scenario:

Help I have been pwned!!! Very sensitive data has been stolen from my machine through a keylogger back to the attacker’s machine. Can you help me find out what they stole?

There were five important things they took, but all I could gather was this pcap.

### Key Items to look at:

Keylogger – once you look at this pcap some more you will see that each keystroke is sent over the wire **individually**.

* More on this later as this is the big factor that will cause many headaches
* Five import things – there are five flags in this pcap

### **First look at the Pcap:**

* There are 2776 Packets
* 5 total endpoints, but only 2 really matter 192.168.56.101 and 192.168.56.104

![](<../.gitbook/assets/image (31).png>)

There are 10 TCP ports in question

![](<../.gitbook/assets/image (27).png>)

There are also 9 UDP ports that were utilized

![](<../.gitbook/assets/image (10).png>)

## Logged 1

What is the Unencoded Flag?

For this one we will focus on port 5146 -> 6415 communication.

This channel had unencrypted letters in the tcp data field that we can look at and it also has fivectf{} which is out flag we are looking for in this challenge set.

![](<../.gitbook/assets/image (30).png>)

![](<../.gitbook/assets/image (29).png>)

![](<../.gitbook/assets/image (34).png>)

### Wireshark Filter:&#x20;

```
(tcp.port == 5146) && (data.data)
```

This filter will drop all the tcp handshakes that gets generated for each letter being sent over the wire. (You cannot just follow the tcp stream as each key is its own stream)

![](<../.gitbook/assets/image (33).png>)

### Tshark:

To make this a little easier we can use tshark on linux to extract the fields and convert the hex data to ascii with the following command:&#x20;

```
tshark -r logged.pcapng -Y "(tcp.port == 5146) && (data.data)" -T fields -e data.data | xxd -r -p
```

tshark -r logged.pcapng  : This will read in the pcap

\-Y "(tcp.port == 5146) && (data.data)"  : This is the display filter

\-T fields -e data.data : this extracts the hex from the data field only

\| xxd -r -p : this converts the hex to ascii to read

![](<../.gitbook/assets/image (22).png>)

## **Distractors**

Other items that are in plain text that were mostly distractors:

9300 -> 9200 StopLookingatmeswan

4445 -> 4444 I need my snacpck

54321 -> 31337 and 60333 -> 1337 i\_am\_all\_about\_that\_basw

But there was a hint in these and the last one being all about that basw or **base is key here as the rest of the encoded flags are various forms of base encoding.**

You can use the tshark code above to extract each of the following encoded flags or at least adjust for any udp transmissions

For the rest of these the order of answering them will not be determined until you pull out the flag, then the questions will make sense, but a **fully decoded flag will start with fivectf{**

## What is base encoding?

A binary-to-text encoding is encoding of data in plain text. More precisely, it is an encoding of binary data in a sequence of printable characters. These encodings are necessary for transmission of data when the channel does not allow binary data (such as email or NNTP) or is not 8-bit clean.

[https://en.wikipedia.org/wiki/Binary-to-text\_encoding](https://en.wikipedia.org/wiki/Binary-to-text\_encoding)

**All of these encoding were done with CyberChef so you could manually do this with cyberchef from the pcap without the use of tshark.**

## Logged 2

What is the flag that is getting 1/3 of the answers right on a test?

Port 8888 -> 45006 over UDP

### Wireshark filter:&#x20;

```
(udp.port == 45006) && (data.data)
```

### Tshark filter:&#x20;

```
tshark -r logged.pcapng -Y "(udp.port == 45006) && (data.data)" -T fields -e data.data | xxd -r -p | base32 -d
```

![](<../.gitbook/assets/image (32).png>)

![Extra Padding](<../.gitbook/assets/image (19).png>)

![](<../.gitbook/assets/image (13).png>)

**This part is trial and error with the various versions of base and make sure you are decoding each keystroke individually.**

### base32 decoding&#x20;

```
tshark -r logged.pcapng -Y "(udp.port == 45006) && (data.data)" -T fields -e data.data | xxd -r -p | base32 -d
```

![](<../.gitbook/assets/image (23).png>)

## Logged 3

Can you find the flag that is tied to cryptocurrency?

Port 8080 -> 8080 over TCP

### Wireshark Filter:&#x20;

```
(tcp.port == 8080) && (tcp.payload)
```

### tshark filter:&#x20;

```
tshark -r logged.pcapng -Y "(tcp.port == 8080) && (tcp.payload)” -T fields -e data.data | xxd -r -p
```

![](<../.gitbook/assets/image (25).png>)

![](<../.gitbook/assets/image (26).png>)

For this one you can use cyberchef or [https://github.com/keis/base58](https://github.com/keis/base58) to install on linux.

```
git clone 
https://github.com/keis/base58

cd base58/

sudo python3 setup.py install
```

This will allow you to | base58 -d (due to the { } in the stream you will not be able to decode it directly in one line

I was able to save the output from this tshark line:

### Tshark

```
 tshark -r logged.pcapng -Y "(tcp.port == 8080) && (tcp.payload)” -T fields -e data.data | xxd -r -p
```

![](<../.gitbook/assets/image (18).png>)

Then remove the {} and I made a short script to loop through the file I saved above and convert each letter from base58 (**b58**)

### Readline Script:

```
#!/bin/bash

FILE=$1
while read LINE; do
     echo $LINE | base58 -d
done < $FILE
```

then I gave chmod +x to the loop file and fed it in the b58 file that I made minus the { }

I ran **./readline b58** this gave me the following: fivectfis\_58\_worth\_more\_than\_32 and you add in the {} to get the flag: **fivectf{is\_58\_worth\_more\_than\_32}**

## Logged 4

Find the flag that is close to 8\*8.

Port 5003 -> 5353 over TCP

### Wireshark Filter:&#x20;

```
(tcp.port == 5003) && (tcp.payload)
```

### Tshark Filter:&#x20;

```
tshark -r logged.pcapng -Y "(tcp.port == 5003) && (tcp.payload)" -T fields -e tcp.payload | xxd -r -p
```

![](<../.gitbook/assets/image (11).png>)

I did not find any good bash commands for base62, there were php and python modules that would work if you wanted to try it out.

For this one you can solve with cyberchef going one key stroke at a time. But from tshark here is the output we get:

![](<../.gitbook/assets/image (35).png>)

when you decode these one at a time in cyberchef you get: **fivectf{`62_is_almost_8_squared`}**

## Logged 5

Find the flag that would give you a B on a test.

Port 8585 -> 1234 Over TCP

### Wireshark Filter:&#x20;

```
(tcp.port == 8585) && (tcp.payload)
```

### Tshark filter:&#x20;

```
tshark -r logged.pcapng -Y "(tcp.port == 8585) && (tcp.payload)" -T fields -e tcp.payload | xxd -r -p
```

![](<../.gitbook/assets/image (17).png>)

For this one in cyberchef you need to select the **base85** module and specifically the **z85 (ZeroMQ**) version

Our tshark output is as follows.

![](<../.gitbook/assets/image (28).png>)

and the flag when you decode it will be: **fivectf{`finally_passing_school_with_an_85`}**
