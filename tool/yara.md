# Yara

YARA is a powerful swiss army knife of pattern matching.

[https://virustotal.github.io/yara/](https://virustotal.github.io/yara/)

YARA is a tool aimed at (but not limited to) helping malware researchers to identify and classify malware samples. With YARA you can create descriptions of malware families (or whatever you want to describe) based on textual or binary patterns. Each description, a.k.a rule, consists of a set of strings and a boolean expression which determine its logic.

YARA is multi-platform, running on Windows, Linux and Mac OS X, and can be used through its command-line interface or from your own Python scripts with the yara-python extension

Yara Documentation: [https://yara.readthedocs.io/en/stable/](https://yara.readthedocs.io/en/stable/)

This tool can be used to match patterns and recurse through a file system.

I will walk through how to solve all of these using this one tool!

Download the 4.0.2 version:

[https://github.com/virustotal/yara/releases/tag/v4.0.2](https://github.com/virustotal/yara/releases/tag/v4.0.2)

On my instance I needed the vc\_redist.x64.exe for c++ installed also:

### How to run a YARA rule

&#x20;

yara \[OPTIONS] RULES\_FILE TARGET

&#x20;

Call the executable ./yara64.exe or ./yara32.exe

Put in the options you want

\-          r to recurse

\-          s for string match

\-          c count

\-          i=\<identifier> for only that identifier

Name of the .yar file you will search for

Name of folder/file to start the search at

## Level 1

### Scenario

Santa had a backlog of toys that needed to be made, so he had to put his crew of elves to work. He assigned each elf randomly three items to make and handed them a piece of paper, but he forgot to write down the totals for toys that he assigned them. You have been tasked with creating an inventory.

#### Question 1

Can you tell Santa how many Dominoes that were made?

Here is the .yar file to find the dominoes

**YARA Rule**

```
rule Dominoes
{
    strings:
            $dominoes = "Dominoes" nocase wide
    condition:
            any of them
}
```

&#x20;

This file will search for anything that matches “Dominoes”

nocase = case insensitive

wide = The wide modifier can be used to search for strings encoded with two bytes per character, something typical in many executable binaries.

Calling this file to run:

```
.\yara64.exe -r .\q1.yar .\
```

Output:

![](<../.gitbook/assets/image (24) (1).png>)

The great thing about YARA is that it tells you the files that an item is located at!

![](<../.gitbook/assets/image (12) (1) (1).png>)

#### Question 2

Santa needs a total count of Sound toys that were made. How many Sound toys got made?

You can also search with hex!

Here is a rule file using hex to find all the Sound toys:

**YARA Rule**

```
rule sound
{
    strings:
        $sound = { 53 00 6f 00 75 00 6e 00 64 }
    condition:
        any of them
}
```

This is the HEX for Sound and the \x00 between the characters is the same as the wide function above (I am still unsure as to why these are requiring this but it returns what we want)

Calling the rule to scan the items:

```
.\yara64.exe -r .\q2.yar .\
```

![](<../.gitbook/assets/image (18) (1) (1) (1).png>)

To count the occurrences | measure can answer that:

.\yara64.exe -r .\q2.yar .\\|measure

![](<../.gitbook/assets/image (3).png>)

![](<../.gitbook/assets/image (22) (1).png>)

#### Question 3

One of Santa's favorite toys is a toy train, how many Toy Trains were made?

You can also have multiple strings that you may want to match, and you can use the condition to force a match of 1 or more strings.

Here is a YARA file that will require the matching of two strings:

**YARA Rule**

```
rule train
{
   strings:
      $train1 = "toy" nocase wide
      $train2 = "train" nocase wide
    condition:
      2 of ($train*)
}
```

This will look for the string toy and train and the condition that must be met is the matching of both strings to be considered true.

![](<../.gitbook/assets/image (7) (1).png>)

You and add the -s option to show the strings that were matched:

![](<../.gitbook/assets/image (1).png>)

Here we see all the \x00 in between all the characters, you could replace them in the pipeline if you wanted to hide them.

.\yara64.exe -rs .\q3.yar .\\|%{$\_ -replace "\\\x00",""}

![](<../.gitbook/assets/image (6) (1) (1).png>)

![](<../.gitbook/assets/image (11) (1).png>)

We can see that toy gets matched twice in this file but train only one time but since both are matched then we have a true condition.

### Level 2

#### Scenario

Santa outsourced this list making ability to head elf, Peppermint Candy, but her ability to spell seems off. Can you help Santa Make use of her notes to the elves?

This time around the files seem to have lots of extra characters we will have to hunt through.

![](<../.gitbook/assets/image (14) (1).png>)

Do you see a pattern?

You should see that the vowels have been randomly modified with extra character substitutions

Substitutions are 1-3 before and/or after the vowel of the extra characters

A – @

E - !

I - 1

O – ()

U - n

#### Question 4

Santa was hoping for lots of small toy to cut out of control costs, how many Shopkins were made?

We need to make rules that will find the strings with any of the variations.

We need to be able to find sh()()()o()()()pk111i111ns for this. So, the following rule will allow us to find that with regular expressions.

\*- 0 or more instances

\+- 1 or more instances

**YARA Rule**

```
rule shopkins
{
    strings:
        $shopkin = /sh\(*\)*\(*\)*\(*\)*o\(*\)*\(*\)*\(*\)*pk1*i1*ns/ nocase wide
    condition:
        any of them
}
```

.\yara64.exe -r .\q4.yar .\elves\_level2\\

![](<../.gitbook/assets/image (16) (1).png>)

.\yara64.exe -rs .\q4.yar .\elves\_level2\\

![](<../.gitbook/assets/image (13) (1) (1).png>)

.\yara64.exe -rs .\q4.yar .\elves\_level2\ | %{$\_ -replace "\\\x00",""}

![](<../.gitbook/assets/image (15) (1).png>)

![](<../.gitbook/assets/image (20) (1) (1) (1).png>)

#### Question 5

Santa wants kids these days to be creative, how many Creative toys got made?

So, this time we have creative toys we are looking to match.

We need to match Cr!!e!!@@a@@t11i11v!!e!!

**YARA Rule**

```
rule creative
{
    strings:
            $creative = /cr!*e!*@*a@*t1*i1*v!*e!*/ nocase wide
    condition:
            any of them
}
```

&#x20;

![](<../.gitbook/assets/image (5) (1).png>)

![](<../.gitbook/assets/image (21) (1) (1).png>)

![](<../.gitbook/assets/image (2).png>)

![](<../.gitbook/assets/image (23) (1) (1).png>)

In case you missed it, we had one of the elves have 2 Creative toys:

![](<../.gitbook/assets/image (17) (1) (1).png>)

![](<../.gitbook/assets/image (4) (1) (1).png>)

The measure did not take into effect the double match so we had to add 1 to our answer.

#### Question 6

One of Santa's favorite toys to keep his calm is a Fidget Spinner, how many Fidget Spinner were made?

**YARA Rule**

```
rule fidget
{
    strings:
          $fidget = /f1*i1*dg!*e!*t sp1*i1*nn!*e!*r/ nocase wide
    condition:
          any of them
}
```

![](<../.gitbook/assets/image (25) (1).png>)

![](<../.gitbook/assets/image (8) (1) (1).png>)

**Stay Tuned For Levels 3/4**
