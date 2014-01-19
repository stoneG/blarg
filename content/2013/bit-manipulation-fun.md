Title: Bit manipulation fun
Date: 2013-01-30 22:00
Author: Sitong Peng
Tags: bit manipulation, binary, tricks, python, algorithms
Slug: bit-manipulation-fun
Summary: A few bit manipulation tricks

So while I was working on interview prep, I got a bit curious about what cool ways bit manipulation might alter the way we might write code. Here's a few examples of short functions you can write using only bit operations.

Simple stuff first
------------------
The following trick may be standard fare but seeing as I am not a "classically" trained programmer (no degree in computer science), this guy did tickle my fancy.

When you write out a integer in a binary representation, 5 as `0101` for example, the least significant bit (leftmost bit) represents if the integer is odd or even. Of course, bits further left represent powers of two, hence the term "base two".
```
0101 is 0*(2^3) + 1*(2^2) + 0*(2^1) + 1*(2^0)
     or 0*8 + 1*4 + 0*2 + 1*1
     or 5
```
Anyone can write a simple function that tests if a given integer `x` is even. Usually it will involve checking to see if `x` mod 2 is 0.
```python
def is_even(x):
    return (x % 2) == 0
```
But, given that least significant bit of a binary value represents odd or even, we can write that same `is_even` function using the `AND` bit operation. 
```python
def is_even(x):
    return (x & 1) == 0
```
For example 6 & 1 is 0.
```
  0110  <-- 6
& 0001  <-- 1
======
  0000
```
This ends up being the same amount of typing but it's interesting that we have this second option of writing the `is_even` function.

Powerful twos
-------------
If you work on [Project Euler](http://projecteuler.net/) problems frequently, the following shortcut might be beneficial to keep in mind. Here's a function that checks if `x` is a power of two.
```python
def is_power_of_two(x):
    power_of_two = 2
    while power_of_two < x:
        power_of_two *= 2
    return power_of_two == x
```
That's pretty compact but it turns out binary is well suited to figure out if a number is a power of two.
```python
def is_power_of_two(x):
    return (x & (x-1)) == 0
```
Let's see this in action with `x` as some random binary number:
```
  010110000  <-- x
- 000000001
===========
  010101111  <-- x-1
& 010110000
===========
  010100000  <-- x & (x-1)

So, (x & (x-1)) just removed the left-most bit that was equal to 1. But then, when might (x & (x-1)) be 0? That would only happen when x has only one 1 bit, which means that x must be a power of 2.
```

Swapping in place
-----------------
Python is nice in that you never have to use a temporary variable when you want to do variable swaps. This is, of course, not the same in other languages.
```python
x, y = y, x
```
Behind the scenes, Python accesses the values of `y` and `x`, then rotates them as it unpacks and assigns to `x` and `y`. Here's what that looks like when run through the Python disassembler:
```python
import dis

def func():
    a = 1
    b = 2
    a, b = a, b
    a, b = b, a

dis.dis(func)
>
>  3           0 LOAD_CONST               1 (1)
>              3 STORE_FAST               0 (a)
>
>  4           6 LOAD_CONST               2 (2)
>              9 STORE_FAST               1 (b)
>
>  5          12 LOAD_FAST                0 (a)
>             15 LOAD_FAST                1 (b)
>             18 ROT_TWO             
>             19 STORE_FAST               0 (a)
>             22 STORE_FAST               1 (b)
>
>  6          25 LOAD_FAST                1 (b)
>             28 LOAD_FAST                0 (a)
>             31 ROT_TWO             
>             32 STORE_FAST               0 (a)
>             35 STORE_FAST               1 (b)
>             38 LOAD_CONST               0 (None)
>             41 RETURN_VALUE 
```
But, did you know we can actually swap without a temporary variable using `XOR` too.
```
x ^= y
y ^= x
x ^= y
```
To understand what's happening, you should recall that `XOR` is commutative, `x^x` is 0, and `x^0` is `x`. Let's break down the [XOR Swap Algorithm](http://en.wikipedia.org/wiki/XOR_swap_algorithm) again.
```
start   ->  breaking down  ->  commuting      ->  cancelling ->  removing 0's
=============================================================================
x ^= y  ->  x = x^y
y ^= x  ->  y = y^x^y      ->  y = x^y^y      ->  y = x^0    ->  y = x
x ^= y  ->  x = x^y^y^x^y  ->  x = x^x^y^y^y  ->  x = 0^y^0  ->  x = y
```
That's pretty cool but I'll stick to Python's easy swap. It is nice, however, to see how binary manipulation can be used to our advantage in languages that don't have an in-place swap.

Now for the disclaimer. While the XOR swap is pretty nifty, it doesn't have much practical use. If you actually plan on using it in the real world, hopefully your application fits in one of [these](http://en.wikipedia.org/wiki/XOR_swap_algorithm#Reasons_for_use_in_practice) buckets. Turns out, optimizing compilers tend to make temporary variable swaps pretty efficient, often faster than this XOR swap.

<span id="note">Have any more bit manipulation tricks you'd like to share? Leave me a comment, I'd love to see more in action.</span>

