---
title: "Hello World"
date: "2019-10-30"
layout: single
comments: true
share: true
related: true
author_profile: true
tags:
- Hello
- Knowledge
categories:
- Hello
---

Hello and Assalamualaikum. This will be my first post and I'm getting used to it since I always refer to this [cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet "Markdown Cheatsheet") and yeah, Welcome!

![enter image description here](https://lh3.googleusercontent.com/meeiESMrPYV32sTLdvoUSfZbePGJiQshKJzGZCu-8-SB0IHSBtWLKl9v05cpU7ecz85KJ8duFwk_){: .align-center}

So, today I’ll be focus only on reversing challenge because these challenge are the only challenges I solved, while the others challenges were solved by Imran.

Let’s begin!

 1. [Rev 101](#rev-101)
 2. [Small Pieces](#small-pieces)
 3. [Handler](#handler)

![enter image description here](https://lh3.googleusercontent.com/GoP5hZP-aPQlypYf4dDamkm2W57q2k98rM0scPeYciE4jUGHS8iZm1lLo-fswToqbO1ybon9Ywjo){: .align-center}

---
# Rev 101

Reverse-101.bin is a 64 bit ELF executable.

![enter image description here](https://lh3.googleusercontent.com/6ag3w70Bu_D9tD8dW-VZYqTryv9kGiWoRSWB7g7zTk0JTrQjLuejkNOE1Cttf8JALDXeBS7xUfi_){: .align-center}

First thing first, I do run the binary on my Linux to check what will their return if I’m input something for the binary.

Usually when I’m doing reverse, I’ll do both static and dynamic analysis on the binary I working for. So, in this case, I’ll use **IDA pro** for static analysis and **GDB-debugger** for dynamic analysis.

Open up IDA, and let’s start take a look on main function.

![enter image description here](https://lh3.googleusercontent.com/k-XxYeWy9jFI6RNjI0R15sO7ZMRhPLIiMLMa5o5H5YqSkCy_GFujTf6yvThx36xKYjUIWSV9CZSz){: .align-center}

On the main function, we can see the strings like `“Try harder!!”` and `“Think out of the BOX!”` that program return if we put some input into it. As we can see, after print out the strings, all of them will jump into the **loc_82B** function.

![enter image description here](https://lh3.googleusercontent.com/obWxprjgoX_OhIeXtdSXxGZrRf_yGwDx6eC30SqLMwJ-sUsaJ727y1bYcJmxGNc7rcPI6ASDzLDJ){: .align-center}

In **loc_82B** function, there is a lot of `LEA` instruction onto the `RAX` registry and then, it will put the value into our stack. Based on the strings character, it’s look like a random strings to me. But also, it could be important too because after the `LEA` instructions, the program make a `MOV` instruction for the flag format `CTCTF{` characters into the stack too.

![enter image description here](https://lh3.googleusercontent.com/09qHquGZrsRRA3B0mBdcy-0t0ReQDjNbUYHVimfzB9f44kKmV_9sle1v5rcervdMajrfxkHZ1ANV){: .align-center}

Looking into the next subfunctions...

![enter image description here](https://lh3.googleusercontent.com/FXLsBjDwPDXU8dBsBkDv7_5v47g9jRoTuyt20wGxFL_s9s5gPvLJlUGvjqZNLC24hryIuGJDEk2J){: .align-center}

The  **loc_A3A** function will compare the length of a strings at  `[rbp+var_12C]` with `30`, where I think this must be the length of the flag. If the length equal to 30, then it will go to red arrow function which is the flag format last character, “}” and then the program exit. And if it’s not, it will got to green arrow functions.

While on the left side (green arrow), we can see there will be a loop process on the program, which is this must be an algorithm to get the character by character of our flag.

Open up GDB, let’s examine the left side function.

By debugging the `loc_9E1` function and observe the changes in `RDX` registry. I noticed, the characters in the `RDX` look alike a character for a strings which is maybe the flag we looking for.

![enter image description here](https://lh3.googleusercontent.com/YjwHwatGPx1XtQg75XXOvpAlfAZb19NUq0INyJfbY4W15EHm0d8a4WP-GEjVT_tudz_3N397iEKI){: .align-center}

So, the first char is “T”. Let’s continue debugging it to get the second char.

![enter image description here](https://lh3.googleusercontent.com/SaWWdTFAruWjAJ2mS1AvEfP63KTmiDpcUIHtSd42uLrgRFr7-J4HVxCuy6fzn2NgQbHp8mzbGjNt){: .align-center}

So, we got the second char which is `“h”`.

So, from here, I examine the  `RDX`  registry until I get the full flag strings by set the breakpoint on offset  `0x555555554a20` which is the offset where `RDX`  changing their value (flag char) and then continue the program with command `c` to get the next char.

After the last char, “g”.. the program will be exited normally.

So based on our examination in  `loc_9E1` function , the full strings will be “`Th1S_1S_4N_e4Sy_R3v3Rs1ng`“.

Put it into the flag box with flag format  **CTCTF{}**, and the answer will be correct!

---
# Small Pieces
For this challenge, it were quite easy than the challenge before.

Run the program, and it’s only return a “`D0n3!`” text.

![enter image description here](https://lh3.googleusercontent.com/Ypjxg4-p8XmFLiJclULKC6MJLMzpzoqAAtEP55kaXjxmc_7cjOzGTjEo-4UNX2FdxlfrQ8dDETir){: .align-center}

Fire up GDB, then I start digging more on the main function.

![enter image description here](https://lh3.googleusercontent.com/yeHDw6bYdsOa6FA6VUfQZg6fJWnWJAk7L8kBKHp3BpXogcI6r2t90_3XnyD9a-Uyc84083Z3dOcI){: .align-center}

Ok now, by using GDB, set a breakpoint on **main** function and then I run the program. So now, it will pause on top of the instruction in main function.

After that, based on Image above, I set a breakpoint after `<part4>` function which is on offset `0x00005555555553cb` and we can see the flag in `RDX` registry by just run the program after that.

![enter image description here](https://lh3.googleusercontent.com/7d5oz49bDemdJV5BtAMnm-d4Ny3JwNUvoK0VZTydIl8hgojyy6m8ooKmj8k1l3XPo0f6zuY-_kFc){: .align-center}

The red pen is our break point and the blue pen is the flag we looking for.

Easy right?

---

# Handler
This challenge should be hard I think. But I don’t know why I got the flag for this challenge only in 5 minutes. Maybe I’m doing it using unefficient way.

Like the other challenge, let’s run the program first.
![enter image description here](https://lh3.googleusercontent.com/pPM1xXqNErdBIfKdaM2L8IbbPm74Ca9h0mK319Q6jyPbA_5q3sXo0ufIhhqird-NZ7mt1WVzCsQk){: .align-center}

After that, let’s do static analysis on the binary using IDA.

![enter image description here](https://lh3.googleusercontent.com/IjqYLogKY27zkw7zqV29lUtDNDspzTOoxYg5kRM4M6fRkrHFsnuk3XBgzmstbRIF-yiiSot_feVl){: .align-center}

In the function list, the most interesting function is `getm3flag` function. I think this function will print out the flag we looking for.

![enter image description here](https://lh3.googleusercontent.com/QAJOVCrRPO_rrZUKwxXKeQeHobnSBQIBhMdkGeOL-9L9IQ_WHnTu2_fY-X4YzwQkL16vbjYFqhDU){: .align-center}

Let’s open up GDB, break on main.

Run the program and the program will pause on our main function.

From here, set the instruction pointer by jumping to that  **getm3flag**  function and then, we will get the flag.

![enter image description here](https://lh3.googleusercontent.com/68FopwFNNRLMk-MHPO2_nPLzpZEEep7dXO2d8tUYiud_w88CUaEkzhE96jgcYDcUl-j8wZfyvCtj){: .align-center}

  
The flag is `CTCTF{N3v3r_g1V3_uP#_#}`
