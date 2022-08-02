# 0 Day

## Question 1:

### What is the Volatility profile for this image?

#### vol.py -f 0day.bin kdbgscan or imageinfo

![](<../.gitbook/assets/image (19) (1).png>)

Answer is Win2012R2x64

## Question 2:

### What was the startup time for this system?

Start up time of the system should be ties to the start time of PID 4, in Volatility we can use the pslist or pstree module

#### vol.py -f 0day.bin --profile=Win2012R2x64 pslist

![](<../.gitbook/assets/image (8).png>)

Answer: 2019-10-17 21:36:43

## Question 3:

### What is the System's Hostname?

One good place to look at for this information would be located in the Widows Registry at the key located at: HKLM:\System\CurrentControlSet\Control\Computername\ActiveComputerName

In volatility in order to find the location of the Windows Registry we need to first find the hive locations.

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 hivelist

We needed a more specific profile for the hivelist module to populate the list

![](<../.gitbook/assets/image (18) (1).png>)

Now we will use the virtual offset of registry\machine\system to get to the list we need: 0xffffc001cb428000

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 printkey -o 0xffffc001cb428000

![](<../.gitbook/assets/image (23) (1).png>)

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 printkey -o 0xffffc001cb428000 -K CurrentControlset

![](<../.gitbook/assets/image (30) (1).png>)

Since Current Control Set does not have anything inside other than a pointer, we will try to controlset001 location.

We will run the following command to get the host name (“ “ around the -K parameter is required).

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 printkey -o 0xffffc001cb428000 -K “ControlSet001\Control\ComputerName\ActiveComputerName"

![](<../.gitbook/assets/image (9).png>)

Answer: WEB-SERVER-DMZ

## Question 4:

### What is the name of the Active Directory Domain?

We can piggyback off of 0day 3 with the Registry Keys as we can find the domain in the current location: hklm:\ SYSTEM\ControlSet001\Services\Tcpip\Parameters

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 printkey -o 0xffffc001cb428000 -K "ControlSet001\Services\Tcpip\Parameters"

![](<../.gitbook/assets/image (6).png>)

Answer: area116.mil

## Question 5:

### What is the system IP address?

For this one we will use the netscan plugin to answer this question, but there are to many local results s owe should grep those results out of the output.&#x20;

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 netscan | grep -v :::0 | grep -v 0.0.0.0

![](<../.gitbook/assets/image (4) (1).png>)

Answer: 172.116.5.105

## Question 6:

### What SID proves session 4 is granted Domain Admin rights?

We will choose the getsids plugin to get this info:&#x20;

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 getsids | grep -i domain

The S-1-5-21 -\*-512 is the Sid used by domain admins where \* is the specific domain

![](<../.gitbook/assets/image (10) (1).png>)

Answer: S-1-5-21-4092088994-1057394591-2624646455-512

## Question 7:

### This system has been exploited. Wat is the PID of the exploited process?

A good place to start on this would be to look at the malfind plugin to see if there are any injected code blocks.

I ran the following command to get the Pids of all possible injections:&#x20;

vol.py -f 0day.bin --profile=Win2012R2x64\_18340 malfind | grep Pid

![](<../.gitbook/assets/image (20).png>)

Doing a quick google search on this one: Winsw.exe

![](<../.gitbook/assets/image (5) (2).png>)

Answer: 1884

## Question 8:

### What PID proves the attacker has the ability to remotely copy files to/from this system?

For this challenge we are looking for a way to send/receive data, we will look as the pstree again.

#### vol.py -f 0day.bin --profile=Win2012R2x64\_18340 pstree

![](<../.gitbook/assets/image (32) (1).png>)

We might first look at the 1884 PID for program, but none of those are the answer, but if we move up to the parent process of 1884, we are at Pid 492. Inside of Pid 492 we have an RDP session and in order to move files between the client and server rdpclip.exe is spawned. Pid 2784 gives the ability to copy to/from the server in the RDP client.

&#x20;The answer is: 2784
