---
title: "An Interesting Problem"
published: true
---

This is a slight bit more technical than usual but it'll be doing my best to explain it as clearly as possible.

I want to share something from one of my computer science classes. Our assignment was to write a simple set of functions with only a few bitwise operators. (eg. You have to check whether an input is `0` using only `<< >> ^ +`).
After writing these functions we could check them using a provided test file. This file would run a variety of test functions to verify our solutions worked, and then would produce a score.

One problem was called `fits_bits`, the goal was to write a function `fits_bits(int x, int n)` that took in a signed integer `x` and an int `n` from 1 to 32. Our function needed to produce a `1` if `x` could be expressed with `n` bits, and a `0` otherwise. I'm not certain on how public or accessible this full assignment is online so I won't be sharing the restrictions for writing the function, just this input and output. 

![explanation of this idea (not necessary to understand)](/assets/various/an-interesting-problem/BinaryNumbers.png)

I ran into a weird issue when working on this function. I used the test file provided to check my work, and had this error: `fits_bits failed. input (0, 32) expected 0, got 1`. Let's unpack this. So we have an issue with our function `fits_bits`, the file tried our function with the input `(0, 32)` (can the number 0 be represented with 32 bits?) and got `True` from my function, but expected `False`. This doesn't really make sense, you only need 1 bit to represent 1 or 0 (and you could argue 0 bits is just 0).


After some trial and error, I was fairly confident I wasn't misunderstanding anything and that there was an issue with the file used to check our answers. 

## The Structure of The Test File

Instead of a big list of inputs and outputs to check against, there were just inputs and test functions. Your solutions were tested against these functions to determine if they were correct. Another interesting fact was that it was compiled before testing your solutions. Here is the test function for `fits_bits`. See if you can spot the issue, and why the input `(0, 32)` might cause it.

```c
int test_fitsBits(int x, int n)
{
  int TMin_n = -(1 << (n-1));
  int TMax_n = (1 << (n-1)) - 1;
  return x >= TMin_n && x <= TMax_n;
}
```

Don't worry if you can't figure it out. It took my professor and I a fair bit of testing and editing (and frustration) to find out what was wrong. Let me walk you through that process.

First I took this snippet from the test file and put it into a new `.c` file to play around with it. We tried compiling it as normal with gcc, there were no compiler errors found and it returned `1`. Success! But why doesn't the same function produce this result when run as part of our test file?

Let's consider what could be different for the test file. Maybe the function is being compiled with different flags? I tried compiling with optimizations and suddenly the function began returning `0` again (the wrong value). Why could optimizations be changing our code?

A compile trying to optimize our code notice some potential issues.

```c
int a[5] = 15;

int b = 0;  //this variable is never used

int c = a[4] + 5; 

printf("%d\n", c);
```

```c
int a[5] = 15;

int c = a[4] + 5;  //a[4] will always be 15

printf("%d\n", c);
```

```c
int c = 15 + 5;

printf("%d\n", c); //c will always be 20
```

```c
printf("%d\n", 20);
```

This doesn't change the actual behavior of our code, and makes it much faster when running. But to do these optimizations the compiler assumes that we've written our code correctly. What bad assumptions are lurking in our code? Let's keep digging.


## A Tip When Debugging

Your compiler is smart, but not *that* smart. It will warn you as much as it can about what inputs and outputs you're using, but when you have a variable set somewhere else, the compiler isn't able to test as effectively.

```c
12 | int a[15] = {0};
13 | 
14 | int b = a[200];
15 | 
16 | printf("%d\n", b);
```

The above code has an obvious error in it, and our compiler agrees.

```
test_file.c:14:14: warning: array subscript 200 is above array bounds of ‘int[15]’ [-Warray-bounds]
   14 |     int b = a[200];
      |             ~^~~~~
```

But when we write the following code: We don't get the same error.

```c
int index_a(int i)
{
    i *= 100;

    int a[15] = {0};

    return a[i];
}
```

This is because our compiler can't test inputs. So let's see what happens when we do the same thing for our test function. 

```c
int test_fitsBits(int x, int n)
{
  int TMin_n = -(1 << (32-1));
  int TMax_n = (1 << (32-1)) - 1;
  return x >= TMin_n && x <= TMax_n;
}
```

```
test_file.c:5:16: warning: integer overflow in expression ‘-2147483648’ of type ‘int’ results in ‘-2147483648’ [-Woverflow]
    5 |   int TMin_n = -(1 << (32-1));
      |                ^
test_file.c:6:3: warning: integer overflow in expression ‘-2147483648 - 1’ of type ‘int’ results in ‘2147483647’ [-Woverflow]
    6 |   int TMax_n = (1 << (32-1)) - 1;
      |   ^~~

The reason why -( -2147483648 ) == -2147483648 is left as an exercise for the reader
(google 2's compliment)
```

We're running into `signed integer underflow`, which is [undefined behavior](https://en.cppreference.com/w/cpp/language/ub) in c. We're doing something that, depending on the hardware and compiler flags, could result in different operations being performed. But this isn't a mistake in the original code, we actually *want* to underflow this value. How do we communicate this to the compiler?

Underflow is only a problem with `signed` integers (numbers that can be negative). If we tell the computer that we want to work with unsigned values, we're letting the compiler know that we can let this number over or under flow with expected behavior.

The fix is to add a `u` for `unsigned` after two of the 1s in the function.

```c
int test_fitsBits(int x, int n)
{
  int TMin_n = -(1u << (32-1));     //this line has been modified.
  int TMax_n = (1u << (32-1)) - 1; //and so has this one
  return x >= TMin_n && x <= TMax_n;
}
```

And here is the fixed function

```c
int test_fitsBits(int x, int n)
{
  int TMin_n = -(1 << (n-1));
  int TMax_n = (1 << (n-1)) - 1;
  return x >= TMin_n && x <= TMax_n;
}
```

That's the end of this journey! Adding just two letters to a 200 line `c` file was all it took to fix this bug. I hope you found this interesting and gave you a few more problem-solving tools for the future!

If you want to stay up to date with everything I do online, including these blogs, check out my [Discord Server](https://discord.com/invite/YUECSUHHM8)!