---
description: Creator's Cut
---

# Cain's Memory Loss

## Question 1:

The Horadrim's network was breached by one of the Prime Evils. We grabbed a memory capture of the computer, can you help us figure out how bad the situation got? Will you be our Hero?

Grab you sword, bow, wand or staff and helps us rid this land of evil.

What is the computer's name?

First we need to get the profile of the image:

```
python vol.py -f Cains_memory.dmp imageinfo



Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
                     AS Layer1 : IA32PagedMemory (Kernel AS)
                     AS Layer2 : WindowsCrashDumpSpace32 (Unnamed AS)
                     AS Layer3 : FileAddressSpace (Cains_memory.dmp)
                      PAE type : No PAE
                           DTB : 0x39000L
                          KDBG : 0x8054cde0L
          Number of Processors : 1
     Image Type (Service Pack) : 3
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2020-09-23 15:26:49 UTC+0000
     Image local date and time : 2020-09-23 10:26:49 -0500
```

Profile is WinXPSP2x86

Next we need to dump the hive list so we can find where the system hive is located in the memory image.

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 hivelist
```

Here is the output where we find the offset to the security hive

```
Virtual    Physical   Name
---------- ---------- ----
0xe1035b60 0x02834b60 \Device\HarddiskVolume1\WINDOWS\system32\config\system
```

We are going to look at the virtual offset: 0xe1035b60&#x20;

Now we can look up the active hostname of the device in the registry:

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 printkey -o 0xe1035b60 -K "ControlSet001\Control\ComputerName\ActiveComputerName"

Values:
REG_SZ        ComputerName    : (V) DECKARD
```

## Question 2:

What Prime Evil has created an account?

For this we will use the Volatility plugin hashdump to pull a list o all the users and hashes

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 hashdump
```

We get the following info:

```
diablo:1005:fbdedec9e0d024658358f3d2c80c1dc5:364099c9dcb9e341b87d1348c4297d6c:::
```

## Question 3:

What is Diablo's Password?

We can save the previous line above into a file called hash and run John the Ripper on it.

```
john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=NT
```

Import to call the format as NT otherwise it will crack the LanMan hash and will cut off after 7 letters not giving the full password.

## Question 4:

Diablo was taking notes on the system. What did he write?

The hint on this was notes, so we can pull up the notepad module from volatility to see what he wrote down.

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 notepad 
```

```
Process: 1744
Text:
  __ _                _    __   ___                        _            _             ___   
 / _(_)_   _____  ___| |_ / _| / (_)    __ _ _ __ ___     | | ___  __ _(_) ___  _ __ | \ \  
| |_| \ \ / / _ \/ __| __| |_ | || |   / _` | '_ ` _ \    | |/ _ \/ _` | |/ _ \| '_ \| || | 
|  _| |\ V /  __/ (__| |_|  _< < | |  | (_| | | | | | |   | |  __/ (_| | | (_) | | | |_| > >
|_| |_| \_/ \___|\___|\__|_|  | ||_|___\__,_|_| |_| |_|___|_|\___|\__, |_|\___/|_| |_(_)| | 
                               \_\|_____|            |_____|      |___/                /_/  

```

## Question 5:

Diablo had copied some data. What did he last copy?

When you copy something we can think of the clipboard and vol.py has a clipboard module.

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 clipboard
```

```
Volatility Foundation Volatility Framework 2.6.1
Session    WindowStation Format                 Handle Object     Data                                              
---------- ------------- ------------------ ---------- ---------- --------------------------------------------------                                               
         0 WinSta0       CF_TEXT               0x4019f 0xe1b83938 fivectf{i_am_the_prime_evil!}                     
         0 WinSta0       CF_UNICODETEXT        0x401a1 0xe13c2930 fivectf{i_am_the_prime_evil!}
```

## Question 6:

Before Diablo left the computer he left wrote a script. What flag was in the script?

Looking around we stumble across the consoles plugin to see a script that had been ran

```
python vol.py -f Cains_memory.dmp --profile=WinXPSP2x86 consoles
```

```
Volatility Foundation Volatility Framework 2.6.1
Cmd #0 at 0x4f9008: type c:\Prime_Evils\diablo\flag.bat
----
Screen 0x4f2ea0 X:140 Y:300
Dump:
Microsoft Windows XP [Version 5.1.2600]                                                                                         
(C) Copyright 1985-2001 Microsoft Corp.                                                                                         
                                                                                                                                
C:\Documents and Settings\diablo>type c:\Prime_Evils\diablo\flag.bat                                                            
  __ _                _    __   ___   _                 _            _                              _                  __       
 / _(_)_   _____  ___| |_ / _| / / |_| |__   ___     __| | __ _ _ __| | __ __      ____ _ _ __   __| | ___ _ __ ___ _ _\ \      
| |_| \ \ / / _ \/ __| __| |_ | || __| '_ \ / _ \   / _` |/ _` | '__| |/ / \ \ /\ / / _` | '_ \ / _` |/ _ \ '__/ _ \ '__| |     
|  _| |\ V /  __/ (__| |_|  _< < | |_| | | |  __/  | (_| | (_| | |  |   <   \ V  V / (_| | | | | (_| |  __/ | |  __/ |   > >    
|_| |_| \_/ \___|\___|\__|_|  | | \__|_| |_|\___|___\__,_|\__,_|_|  |_|\_\___\_/\_/ \__,_|_| |_|\__,_|\___|_|  \___|_|  | |     
                               \_\             |_____|                  |_____|                       
```
