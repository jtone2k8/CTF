The Heist

https://medium.com/@zemelusa/first-steps-to-volatile-memory-analysis-dcbd4d2d56a1

1. 

The CFO from Turkey Durkey Inc. has reported to you (The CISO) that all the money from the accounts was drained from an unknown source. Your job is to look at this memory dump that was taken from their PC and figure out what happened.

answer: WinXPSP2x86

volatility -f the_heist.vmem imageinfo

![image](uploads/8b448115835c1b15aa0b131154bfa884/image.png)

2.

What Process was ran from the desktop?

answer: reader_sl.exe

volatility -f the_heist.vmem --profile=WinXPSP2x86 pstree

![image](uploads/81fcd6b18f11dc241a98aab908143905/image.png)

3.

How many hidden processes are there?

answer: 0

volatility -f the_heist.vmem --profile=WinXPSP2x86 psxview

![image](uploads/c398d5e2df8d599eb3ed9e4ba834ddd7/image.png)

4.

What is the IP and Port of the Active Connection? flag:ip:port

answer: 41.168.5.140:8080

volatility -f the_heist.vmem --profile=WinXPSP2x86 connscan

![image](uploads/8c11def3a80eba66c1c64e678327dd69/image.png)

5.

What was the full filepath of Reader_sl.exe? C:\some\path\to\the\file.ext

answer: c:\Program Files\Adobe\Reader 9.0\Reader\Reader_sl.exe

volatility -f the_heist.vmem --profile=WinXPSP2x86 cmdline

![image](uploads/b306da2f3267ff5fe159868b2e4042b9/image.png)

6.

What HTTP command is being used by the process in Reader_sl.exe?

answer: POST

volatility -f the_heist.vmem --profile=WinXPSP2x86 procdump -p 1640 --dump-dir .

volatility -f the_heist.vmem --profile=WinXPSP2x86 memdump -p 1640 --dump-dir .

strings 1640.dmp | grep -Fi "41.168.5.140" -C 5

![image](uploads/cfa605bd4f360a9802b4c3c53d642f22/image.png)

7.

What type of websites is this process looking for?

answer: banking

strings 1640.dmp | less

![image](uploads/2e5e41e736b12ae6cd027d54b838e23e/image.png)

8.

How many hits does Virus Total have for the program reader_sl.exe?

answer: 32

![image](uploads/56269a49bf3d69bd67fe4f0a67e954ad/image.png)

9.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425
On the Mitre ATT&K Matrix, what was used for Persistence?

answer: hooking

![image](uploads/ad11b969564c52950463164a4e7773c0/image.png)

![image](uploads/c04ae5a2826ecb9896683a0d259d1aa2/image.png)

10.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425
What Registry key is modified for lateral movement? Format: hive\path\to\registry\key

answer: HKLM\SYSTEM\CONTROLSET001\CONTROL\TERMINAL SERVER\TSUSERENABLED

![image](uploads/88e4476bd74eb528bca9f05764228f69/image.png)

11.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425
What program is called?

answer: Acrobat Viewer

![image](uploads/fe44e0edb42b0ef08dd528db6ef29550/image.png)