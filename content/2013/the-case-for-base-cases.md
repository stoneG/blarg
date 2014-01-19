Title: The case for base cases
Date: 2013-01-28 21:45
Author: Sitong Peng
Tags: base case, python, tower of hanoi, algorithms, recursion, youtube
Slug: the-case-for-base-cases
Summary: Breaking a problem down to its base case and solving the Tower of Hanoi

You have a complicated problem to solve. How do you start tackling it?

One of the most tried and tested methods of problem solving is nothing more than implementing some noise control. In other words, can you peel back the complexity of the problem and make it simpler?

Tower of Hanoi
--------------
I finally sat down and tried solving this problem two days ago. For those of you unfamiliar, the [Tower of Hanoi](http://en.wikipedia.org/wiki/Tower_of_Hanoi) is an old puzzle that has been mostly re-appropriated as a classic example of algorithmic thought. Here's a version of it:

![Tower of Hanoi, from Wikipedia](http://upload.wikimedia.org/wikipedia/commons/0/07/Tower_of_Hanoi.jpeg)

There are three rods and a number of different sized disks that can slide onto any rod. The puzzle starts with the disks in a neat stack, ascending by size, on the first rod. The objective of the puzzle is to move the entire stack to the last rod, obeying the following rules:  

1. Only one disk may be moved at a time.  
2. Each move consists of taking the top-most disk from one of the rods and sliding it onto another rod, on top of the other disks that may already be present on that rod.  
3. No disk may be placed on top of a disk smaller than it.

Seems straightforward enough, but let's solve this for N disks. How should we start?

Let's start with the base cases
-------------------------------
N=1
```
    |         |         |
    |         |         |
   -|-        |         |
========= ========= =========
    A         B         C

1.  -----------------> -|-
Done.

So we solve N=1 with one move of -|-.
```
N=2
```
    |         |         |
   -|-        |         |
  --|--       |         |
========= ========= =========
    A         B         C

1.  -------> -|-

    |         |         |
    |         |         |
  --|--      -|-        |
========= ========= =========
    A         B         C

2.  ----------------> --|--
3.            -------> -|-
Done.

Let's take a closer look at these moves:
1. Exactly like solving N=1, except A->B instead of A->C.
2. The second step places the largest disc at the base of C.
3. Also solves N=1, only B->C instead of A->C.
Done.

Let's rewrite our steps like this:
1. Solve N=1, except A->B
2. Move --|-- from A->C
3. Solve N=1, except B->C
Done.
```
N=3
```
   -|-        |         |
  --|--       |         |
 ---|---      |         |
========= ========= =========
    A         B         C

1.  -----------------> -|-
2.  ------> --|--
3.           -|- <-------

    |         |         |
    |        -|-        |
 ---|---    --|--       |
========= ========= =========
    A         B         C

4.  ---------------> ---|---
5. -|- <-------
6.            ------> --|--
7.  -----------------> -|-
Done.

Notice how steps 1-3 solve N=2 and places it at B. Rewrite?
1. Solve N=2, except A->B
2. Move ---|--- from A->C
3. Solve N=2, except B->C
Done.
```
Interesting. Looks like we have a formula.
```
N=n
1. Solve N=(n-1), except A->B
2. Move largest disc from A->C
3. Solve N=(n-1), except B->C
```
I was pretty excited when I saw this pattern, as it meant I could code up a solution in very few lines of code. Here it is in Python, using a list as a stack:
```python
def solve_hanoi(n, A, B, C):
    if n == 0:
        return
    solve_hanoi(n-1, A, C, B)
    C.append(A.pop())
    solve_hanoi(n-1, B, A, C)
```
The trick is to redefine the rods for each recursive `n-1` call. What's more is you really only need to think about which rod you're starting and ending at. The odd rod out is just the buffer.

I'd show you the code in action but all the computation is behind the scenes and not very interesting. Luckily a quick search on Youtube yielded a visual solution for N=6. Notice how there is a point of symmetry in the middle as the 5 smaller disks sit at B and the largest disk migrates over to C. Utterly awesome!

<iframe style="margin-top:20px;" width="560" height="315" src="http://www.youtube.com/embed/JqC_LsF1iWg" frameborder="0" allowfullscreen></iframe>
