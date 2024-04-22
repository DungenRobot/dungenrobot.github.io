---
title: "An Interesting Problem"
published: false
---

This is a slight bit more technical than usual but it'll be doing my best to explain it as clearly as possible.

I want to share something from one of my computer science classes. Our assignment was to write a simple set of functions with only a few bitwise operators. (eg. You have to check whether an in is 0 using only `<< >> ^ +`).
After writing these functions we could check them using a provided test file. This file would run a variety of test functions to verify our solutions worked, and then would produce a score.

One problem was called `fits_bits`, the goal was to write a function `fits_bits(int x, int n)` that took in a signed integer `x` and an int `n` from 0 to 32. Our function needed to produce a `1` if `x` could be expressed with `n` bits, and a `0` otherwise. I'm not certain on how public or accessible this full assignment is online so I won't be sharing the restrictions for writing the function, just this input and output. 

![explanation of this idea (not necessary to understand)](/assets/various/an-interesting-problem/BinaryNumbers.png)

I ran into a weird issue when working on this function. I used the test file provided to check my work, and had this error: `fits_bits failed. input (0, 64) expected 0, got 1`. Let's unpack this. So we have an issue with our function `fits_bits`, the file tried our function with the input `(0, 64)` (can the number 0 be represented with 64 bits?) and got `True` from my function, but expected `False`. This doesn't really make sense, you only need 1 bit to represent 1 or 0 (and you could argue 0 bits is just 0).


After some trial and error, I was fairly confident I wasn't misunderstanding anything and that there was an issue with the file used to check our answers. 


There was a problem with the 