# Tyrael's Crypto

Here are a bunch of simple crypto challenges:

## Question 1:

Tyrael has chosen you to be a hero of the Horadrim! He has encoded several messages and it is your job to decode these messages to receive your instructions (Flags!). \
\
Here is the first one he left for you to decipher:

```
U3RvcCEgVGhlIGJlYXN0IGNvbnRhaW5lZCBoZXJlaW4gc2hhbGwgbm90IGJlIHNldCBmcmVlIOKAlCBub3QgZXZlbiBieSB5b3UuIGdjdGZ7dGhlX3JldHVybl9vZl9ldmlsfQ==
```

### Question 1 Answer:&#x20;

This one is pretty easy to spot that it looks like base64, throwing this into cyberchef or other base64 decode tool we get this as a decoded message:

```
Stop! The beast contained herein shall not be set free — not even by you. gctf{the_return_of_evil}
```

## Question 2:

Tyrael saw that the first message was too easy for The Prime Evils to decode, so know he must step up his game! \
\
If it wasn't Mephisto or Diablo... then it must have been Baal... or one of them... \
\
Here is the second message he left for you to decipher:

```
H!`l!`!v`oedsds:!H!i`wd!rddo!lnsd!ui`o!lnru!vntme!dwds!hl`fhod/!Ui`u!hr!ldsdmx!lx!o`utsd/!fbugzuxs`dm^hr^lx^o`ld|
```

Here is a [CyberChef Recipe](https://cyberchef.org/#recipe=From\_Base64\('A-Za-z0-9%2B/%3D',true,false\)\&input=VTNSdmNDRWdWR2hsSUdKbFlYTjBJR052Ym5SaGFXNWxaQ0JvWlhKbGFXNGdjMmhoYkd3Z2JtOTBJR0psSUhObGRDQm1jbVZsSU9LQWxDQnViM1FnWlhabGJpQmllU0I1YjNVdUlHZGpkR1o3ZEdobFgzSmxkSFZ5Ymw5dlpsOWxkbWxzZlE9PQ)

### Question 2 Answer:

The key to this challenge was in the line: `If it wasn't Mephisto or Diablo... then it must have been Baal...`` `**`or one`**` ``of them...` \
``\
``With that last bit or one can clue us in that we can try to xor the message with a key of 01

```
I am a wanderer; I have seen more than most would ever imagine. That is merely my nature. gctf{tyrael_is_my_name}
```

Here is a [CyberChef Recipe](https://cyberchef.org/#recipe=XOR\(%7B'option':'Hex','string':'1'%7D,'Standard',false\)\&input=SCFgbCFgIXZgb2Vkc2RzOiFIIWlgd2QhcmRkbyFsbnNkIXVpYG8hbG5ydSF2bnRtZSFkd2RzIWhsYGZob2QvIVVpYHUhaHIhbGRzZG14IWx4IW9gdXRzZC8hZmJ1Z3p1eHNgZG1eaHJebHheb2BsZHw)

