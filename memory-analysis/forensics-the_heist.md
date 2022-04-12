# forensics-the\_heist

The Heist

https://medium.com/@zemelusa/first-steps-to-volatile-memory-analysis-dcbd4d2d56a1

1.

The CFO from Turkey Durkey Inc. has reported to you (The CISO) that all the money from the accounts was drained from an unknown source. Your job is to look at this memory dump that was taken from their PC and figure out what happened.

answer: WinXPSP2x86

volatility -f the\_heist.vmem imageinfo

1.

What Process was ran from the desktop?

answer: reader\_sl.exe

volatility -f the\_heist.vmem --profile=WinXPSP2x86 pstree

1.

How many hidden processes are there?

answer: 0

volatility -f the\_heist.vmem --profile=WinXPSP2x86 psxview

1.

What is the IP and Port of the Active Connection? flag:ip:port

answer: 41.168.5.140:8080

volatility -f the\_heist.vmem --profile=WinXPSP2x86 connscan

1.

What was the full filepath of Reader\_sl.exe? C:\some\path\to\the\file.ext

answer: c:\Program Files\Adobe\Reader 9.0\Reader\Reader\_sl.exe

volatility -f the\_heist.vmem --profile=WinXPSP2x86 cmdline

1.

What HTTP command is being used by the process in Reader\_sl.exe?

answer: POST

volatility -f the\_heist.vmem --profile=WinXPSP2x86 procdump -p 1640 --dump-dir .

volatility -f the\_heist.vmem --profile=WinXPSP2x86 memdump -p 1640 --dump-dir .

strings 1640.dmp | grep -Fi "41.168.5.140" -C 5

1.

What type of websites is this process looking for?

answer: banking

strings 1640.dmp | less

1.

How many hits does Virus Total have for the program reader\_sl.exe?

answer: 32

1.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425 On the Mitre ATT\&K Matrix, what was used for Persistence?

answer: hooking

1.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425 What Registry key is modified for lateral movement? Format: hive\path\to\registry\key

answer: HKLM\SYSTEM\CONTROLSET001\CONTROL\TERMINAL SERVER\TSUSERENABLED

1.

HybridAnalysis.net gives a better understanding of the malware: https://www.hybrid-analysis.com/sample/5b136147911b041f0126ce82dfd24c4e2c79553b65d3240ecea2dcab4452dcb5/5c5f134c7ca3e107a145f425 What program is called?

answer: Acrobat Viewer
