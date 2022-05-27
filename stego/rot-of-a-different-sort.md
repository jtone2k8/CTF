# ROT of a Different Sort

## Scenario

The attached file was created with a ROT program we found online, but ROT13 doesn't work and we're not sure what else to try. Can you help us out?

```
Tig hdthurm DBH qcyhldpek utgg ggzmb etr geksmcjj mef qi ynl rxoqk dv lay yeyqadvhv hlqwq dbhpjvv. Vhqfnuhj rtz zxdlfrr, jhtwzo ymd rxrhzbicszz giycd wjjqphbvvh yu bqo uaqgudwgn kc shf lrike. Dsq taqx rip xpd mqrontn oyc vg hflb_do_y_bjv_hnlmmaoyf_tged_kio13. Ig bsz zwufpp hwyj pcod AxbftFljl qwceqnr ew t nyogotjpj tx xayrdnabyey fvjdszgf, M nvxn jah hxrjx odb qedksi evc edqq lykz nca ASF qukftphnbd!
```

### ROT Types

ROT-13: is a simple letter substitution cipher that replaces a letter with the 13th letter after it, in the alphabet. ROT13 is a special case of the Caesar cipher which was developed in ancient Rome.&#x20;

ROT-47: in addition to scrambling the basic letters, treats numbers and common symbols. Instead of using the sequence A–Z as the alphabet, ROT47 uses a larger set of characters from the common character encoding known as ASCII.

#### CyberChef and ROT

CyberChef is always a good place to start with encoded items. Let’s Mess with ROT13 and ROT47

![Rot 13](<../.gitbook/assets/image (16).png>)

![Rot 47](<../.gitbook/assets/image (12) (1).png>)

![Rot 13 With Numbers](<../.gitbook/assets/image (15).png>)

#### Dynamic ROT?

Hint: Instead of constant rotation being applied, maybe it is a dynamic rotation?&#x20;

How can we figure out a dynamic ROT? You will need to use some programming skills to solve this one!

#### Which ROT to use?

With the first word being three characters long we can mess with the Rotation amount of the two ciphers to see what gives us a word We notice a couple of patterns also that will help us choose which one to go with.

Starting with the Rotation amount of 0, we can see that the first letter is a T

We can go up and down but in cracking a cipher we should start off finding a common word.

We will drop ROT-47 as the punctuation seems to be correct and we don’t want to mess with that

#### Common Words to find the cipher

Let’s go out on a limb and think of common three letter words that start with T

| <p>Tab</p><p>Tag</p><p>Tap</p><p>Toe</p> | <p>Tee</p><p>Top</p><p>Toy</p><p>Try</p> | <p>Tie</p><p>Tea</p><p>Tee</p><p>The</p> |
| ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |

The great thing about Cyber Chef is how easy it is to stuff.

Lets make this simpler and just do the first word.

Vowels – a: -8 e:-4 i: 0 o: 6 u: 12

Third Letter: -8: q -4: y 0: g 6: s 12: e

So we get the following words: Taq Tey Tig Tos Tue

None of those matched our words.

The two words we have left are Try and The.

Try is a ROT13 of 9 so we would get Try – This could be it!!

The is a ROT13 of -1 so we would get The – This could be it also!!

What about the next word – do either of these work out?

\+9 gives us a word of rwvpi – not much luck here

\-1 gives us a word of dynamic – Did we crack the code?

#### Scripting a solution

Pick you favorite scripting language and have a go at it. For this I am going to do PowerShell to solve this problem

![](<../.gitbook/assets/image (20) (1) (1).png>)

To start off I am going to create a variable with the encoded text - $encode

Then I am going to drop the capitalization to make it easier to work with $text

I am creating my final out variable with $out

For the next section we will loop through the $text variable until we reach the end. As we go through each character we want to convert from ASCII to Decimal to make the conversions easier. We will also use our ascii table to help us out. http://www.asciitable.com/

![](<../.gitbook/assets/image (19) (1) (1) (1).png>)

<img src="../.gitbook/assets/image (21) (1).png" alt="" data-size="original">

Now that we have our charters in decimal we can now begin to Rotate our letters. In my script I rotate up (but rotating down -1 will also work)

![](<../.gitbook/assets/image (17) (1).png>)

The main thing here is the % 26 that reset back to 0 if the number goes over the 26 char limit so this will keep us in the ASCII range of 97-122 and we do $t (dec) – the limit to do a -1 rot

Then if the rot is under 97 we just add 26 to get it back in range.

![Full Script](<../.gitbook/assets/image (13) (1).png>)

We run the script and the answer is in the output!

```
$encode = "Tig hdthurm DBH qcyhldpek utgg ggzmb etr geksmcjj mef qi ynl rxoqk dv lay yeyqadvhv hlqwq dbhpjvv. Vhqfnuhj rtz zxdlfrr, jhtwzo ymd rxrhzbicszz giycd wjjqphbvvh yu bqo uaqgudwgn kc shf lrike. Dsq taqx rip xpd mqrontn oyc vg hflb_do_y_bjv_hnlmmaoyf_tged_kio13. Ig bsz zwufpp hwyj pcod AxbftFljl qwceqnr ew t nyogotjpj tx xayrdnabyey fvjdszgf, M nvxn jah hxrjx odb qedksi evc edqq lykz nca ASF qukftphnbd!" 
 
$text=$encode.ToLower()
$out =""
for ( $i = 0 ; $i -le $text.Length ; $i++ ) {
    $t = [int]$text[$i]        
        if ($t -ge 97 -and $t -le 122){
            $charlimit = $i % 26
            $rot=$t-$charlimit
        if ($rot -lt 97){
            $rot +=26
        }
    } else {
        $rot=$t
    }  
    $out += [char]$rot
} 
$out
```
