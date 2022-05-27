# Invoked!

## Scenario**:**

Invoke-Expression can be a sign of a malicious script on a network. With PowerShell this can be obfuscated in several different ways!

Inside this file is a script that will show you 11 ways to call out invoke-expression and most of these will possibly bypass security products.

What is the unencoded flag?

## Key take away for this scenario:

\-          Invoke-Expression can be obfuscated but still run.

\-          We need to decode the flag

This challenge was inspired by the follow article from SANS: [https://isc.sans.edu/forums/diary/Did+You+Spot+InvokeExpression/26762/](https://isc.sans.edu/forums/diary/Did+You+Spot+InvokeExpression/26762/)

## Script**:**

```
$l10="&('DEX'.replace('D','I')) '$l11'; `nwrite-host 'Invoke-Expression # 10';('{4}{15}{25}' -f `$f)"
$l9="&($PSHome[21]+$PSHOme[34]+'x') '$l10'; `nwrite-host 'Invoke-Expression # 9';('{33}{17}{9}' -f `$f)"
$l3="&(('{2}{1}{0}' -f 'x', 'e', 'i')) '$l4'; `nwrite-host 'Invoke-Expression # 3';('{5}{7}{26}{36}' -f `$f)"
$l7= "&( $VERBOSePRefereNCe.toSTRiNG()[1,3]+'X'-join'') '$l8'; `nwrite-host 'Invoke-Expression # 7';('{16}{13}{14}{3}' -f `$f)"
$l6="&(($ENV:COMsPEc[4,15,25])-join '') '$l7'; `nwrite-host 'Invoke-Expression # 6';('{32}{20}{30}{11}' -f `$f)"
$l5="sal turkeyjerky iex; turkeyjerky '$l6'; `nwrite-host 'Invoke-Expression # 5';('{27}{1}{23}{24}' -f `$f)"
$l4="$box = 'ëçú'.ToCharArray();for ($i=0; $i -lt $box.Length; $i++) {$box[$i] = $box[$i] -bxor 0x82 }.(-join($box)) '$l5'; `nwrite-host 'Invoke-Expression # 4';('{28}{19}{29}{35}' -f `$f)"
$l2= "&('i'+'e'+'x') '$l3';`nwrite-host 'Invoke-Expression # 2';('{23}{31}{18}{2}' -f `$f)"
$flag="`nwrite-host 'flag is: n2ltZNWV22fZGhldbln29nZvcNbZtVma2Xp0'"
$f="n2ltZNWV22fZGhldbln29nZvcNbZtVma2Xp0" -split''
$l11= "$String = 'XEI';&(([regex]::Matches($String,'.','RightToLeft')).value -join '') '$flag'; `nwrite-host 'Invoke-Expression # 11';('{22}{6}{21}' -f `$f)"
$l8="&IE`x '$l9'; `nwrite-host 'Invoke-Expression # 8';('{34}{10}{8}' -f `$f)"
.('i'+'e'+'x') '$l2'
```

## Solution**:**

### What is Invoke-Expression

When a PowerShell script is obfuscated, the deobfuscation process is, most of the time, performed through the Invoke-Expression cmdlet. Invoke-Expression evaluates the string passed as an argument and returns the results of the commands inside the string.

### Invoke Expression Walkthrough

#### Level 1:

&#x20;             .('i'+'e'+'x') '$l2'

This concatenates the alias of Invoke-Expression (IEX) and runs the next command which is level 2

#### Level 2:

&#x20;             &('i'+'e'+'x') '$l3'

Duplicate of level 1 but has & instead of . to run the command and calls Level 3

#### Level 3:

&#x20;             &(('{2}{1}{0}' -f 'x', 'e', 'i')) '$l4'

This one is a string jumble where it puts together the following string in the order specified; in this case {2} = I, {1} = e, {0} = x

#### Level 4:

&#x20;             $box = 'ëçú'.ToCharArray();for ($i=0; $i -lt $box.Length; $i++) {$box\[$i] = $box\[$i] -bxor 0x82 }.(-join($box)) '$l5'

This line of code is going through and running an xor of 0x82 in the string: 'ëçú to create iex and then joins them back together from an array

#### Level 5:

&#x20;             sal turkeyjerky iex; turkeyjerky '$l6'

This command is creating an alias for iex of turkyjerky so anytime turkeyjerky is used it is calling iex

#### Level 6:

&#x20;             &(($ENV:COMsPEc\[4,15,25])-join '') '$l7'

This calls out an environmental variable: C:\Windows\system32\cmd.exe and extracts out iex and joins them together

#### Level 7:

&#x20;             &( $VERBOSePRefereNCe.toSTRiNG()\[1,3]+'X'-join'') '$l8'

This one gets the preference above: SilentlyContinue, it extracts out ie and joins them to x and runs

#### Level 8:

&#x20;             \&IE\`x '$l9'

This one runs the \` which is an escape character in PowerShell, typically this is used for escaping \ and special characters that you want to print ou. But since x is not a special character the \` doesn’t do much but obfuscate the code.

#### Level 9:

&#x20;             &($PSHome\[21]+$PSHOme\[34]+'x') '$l10'

$pshome = C:\Windows\System32\WindowsPowerShell\v1.0, so this just pulls out the I and e from another variable

#### Level 10:

&#x20;             &('DEX'.replace('D','I')) '$l11'

This command takes DEX and replaces the D for an I so when ran it becomes IEX

#### Level 11:

&#x20;             $String = 'XEI';&((\[regex]::Matches($String,'.','RightToLeft')).value -join '') '$flag'

This command takes the XEI and reverses it to become IEX

### Getting to the Answer:

We can see that the original order of the script is out of order, so just going through you may get things jumbled.

But essentially to find the flag we need to run the last part of each line in order, all the invoke-expressions did was unscramble the order for us.

For example, we run for the last line we have #2, if we run the code after that separately, we get the following:

![](<../.gitbook/assets/image (17).png>)

or Zml2 which if you don’t recognize this it is base64 for fiv

Let’s look closer at what is going on here

There is a variable called $flag that equals “n2ltZNWV22fZGhldbln29nZvcNbZtVma2Xp0” and in the code above we are calling in the positions of the letters in this jumbled up mess. So, the 23rd letter is Z … and so on (there is an empty space in the front of the letters so that is why it starts at 1 and not 0).

Pulling the letters out in order:

There are several ways to do this…

1. Manually pull them out… but this is time consuming and prone to human error.

```
#Z  m  l  2  Z  W  N  0  Z  n  t  p  b  n  Z  v  a  2  V  f  d  G  h  l  X  2  V  2  b  2  t  l  c  n  N  9
#23 31 18 2  5  7  26 36 28 19 29 35 27 1  23 24 32 20 30 11 16 13 14 3  34 10 8  33 17 9  4  15 25 22 6  21
#n 2 l t Z N W V 2 2 f Z G h l d b l n 2 9 n Z v c N b Z t V m a 2 X p 0
#1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6

```

2\. You can run the echo commands one at a time

![](<../.gitbook/assets/image (18) (1) (1).png>)

3\. Or create a script that will pull it all together and convert from base64 (I like this one!)

```
$answer =""
$answer= ('{23}{31}{18}{2}' -f $f) #Zml2
$answer+= ('{5}{7}{26}{36}' -f $f)  #ZWN0
$answer+= ('{28}{19}{29}{35}' -f $f)#Zntp
$answer+= ('{27}{1}{23}{24}' -f $f) #bnZv
$answer+= ('{32}{20}{30}{11}' -f $f)#a2Vf
$answer+= ('{16}{13}{14}{3}' -f $f) #dGhl
$answer+= ('{34}{10}{8}' -f $f)     #X2V
$answer+= ('{33}{17}{9}' -f $f)     #2b2
$answer+= ('{4}{15}{25}' -f $f)     #tlc
$answer+= ('{22}{6}{21}' -f $f)     #nN9 
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($answer))

```

![](<../.gitbook/assets/image (19) (1) (1).png>)
