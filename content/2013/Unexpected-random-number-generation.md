Title: Unexpected random number generation
Date: 2013-01-23 20:20
Author: Sitong Peng
Tags: random, random numbers, processes, forking, python, whiskey
Slug: unexpected-random-number-generation
Summary: How you might want to be careful with random number generation in child processes

So, last month, I was working on building test applications for [Whiskey](https://github.com/stoneG/whiskey), my Python WSGI server implementation. I wanted to ensure Whiskey was robust enough to keep going, even if it was serving applications that met critical, program-killing exceptions. To that end, I wrote a random exception throwing application to test with Whiskey. Here's the code, simplified and stripped down to its core.

```python
import os
import random

while True:
    pid = os.fork()
    if pid == 0: # if we are in the child process
        print random.random()
        os._exit(0)
```
A little background
-------------------
Whiskey handles concurrent client requests by forking new child processes. Calling `os.fork()` splits the current process into two, the parent and child. The child process will have a process identifier (pid) of 0. Recall that a forked child process has a copy of the same memory space/environment of its parent.

Result
------
```
> 0.844421851525
> 0.844421851525
> 0.844421851525
> 0.844421851525
> 0.844421851525
> 0.844421851525
> 0.844421851525
```

You might not be surprised since you've already come into this post with some suspicion, but remember that this is the stripped down code. What I actually ran into, was an application that either always failed or never failed. So, what's going on?

Well, every client request leads to a forked child process that handles the request. However, each child process ends up having the same random seed, since it is always forked from the same parent process (or more specifically, a parent process which never changes its random seed). This results in a pesky situation where you unexpectedly have an expected random number.

Solution
--------
We need to reset the random seed for each forked process. One way is to set the seed to the time of the request.
```python
import os
import random
import time                      ##### ADDED THIS LINE ######

while True:
    pid = os.fork()
    if pid == 0:
        random.seed(time.time()) ##### ADDED THIS LINE ######
        print random.random()
        os._exit(0)
```
Randomness restored.

<span id="update">Update</span>  
My friend [Geoff](https://twitter.com/geofflee) mentioned that for real applications, you wouldn't want to use `time.time()` as your seed. Besides you leaving yourself open to the chance of two requests spawning processes at the same time, any PRNG (pseudo random number generator) is unsuitable for security reasons. Instead using `/dev/random` (UNIX) or `CryptGenRandom` (Windows) is preferable, since you'd have true randomness then. For the Python implementation, you'd want to make a call to `os.urandom()` to access your OS's random file. You can find the documentation on that, [here](http://docs.python.org/2/library/os.html#os.urandom).

<span id="note">I wish I could say I ran into this recently, but I've unfortunately been too busy applying to jobs instead of working on personal projects. I actually talked about this subject for a Hacker School weekly presentation on December 6th, 2012.</span>
