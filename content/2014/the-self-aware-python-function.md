Title: The Self Aware Python Function
Date: 2014-01-18 8:30
Author: Sitong Peng
Tags: python
Slug: the-self-aware-python-function
Summary: How would you write a function in Python that knows how many times it's been invoked?

I've been using Python for over a year now. It's what I learned to program with. About half a year ago, a fellow coder (friend of a friend) posed the following riddle upon learning that it was my language of choice:

<p class="quote">"How would you write a function in Python that knows how many times it's been invoked?"</p>

Let's consider this for a moment. A function that knows how many times it has been invoked is one that would need some access to a scope outside of it's own. Functions are generally pretty transient &mdash; they do their thing and then wait until they are told to do it again. There isn't a middling dormant state where they consider how many times they've been told to execute their (probably) menial task.

As a first stab, we might consider a global counter, as in the following setup:
```python
count = 0

def self_aware_function():
    count += 1
    if count == 1:
        print 'This is my first time doing this'
    else:
        print 'I have done this {} times now.'.format(count)

for i in range(10):
    self_aware_function()
```
Those familiar with Python are up in arms right now, and rightfully so. Here's what you get when invoking `self_aware_function`:
```shell
UnboundLocalError: local variable 'count' referenced before assignment
```
In Python, when you are in a function and assign a value to an immutable type variable (such as our int `count`), you end up shadowing the global variable with one local to the function's scope. So when `count += 1` is evaluated (remember this is expanded to `count = count + 1`), Python cannot find the value for `count` in the expression to the right of the `=`.

## Okay, local variables won't work, how about we use the `global` keyword or mutable variables?

```python
# global keyword

def self_aware_function():
    global count # Now we force the use of the global count
    count += 1
    # ...
```
```python
# using a list
count = [0]

def self_aware_function():
    count[0] += 1
    # ...
```
These both work and globals were my first idea for a solution. However, when I asked if this was acceptable, I was met with opposition.

<p class="quote">"You are only allowed to write code within the function, not outside of it."</p>

That's rough. How do you become self aware of an outside world/scope that you can't even interact with?

Turns out, the answer lies within a popular Python [gotcha](http://effbot.org/zone/default-values.html).

Self-awareness
--------------

Here's how you define a default parameter in Python:
```python
def foo(default='bar'):
    return default
```
In Python, default parameters are evaluated ONLY once &mdash; when the function is defined. That means the default parameter is "the same" for all invocations of the function. I say "the same" because normally the default parameter will have the same value across multiple function invocations, but if your default parameter is mutable then you change it! And then you can do some really special things.

Case in point, self-awareness:

```python
def self_aware_function(count=[0]):
    count[0] += 1
    if count[0] == 1:
        print 'This is my first time doing this'
    elif count[0] >= 10:
        print "Who are you to tell me what to do???"
    else:
        print 'I have done this {} times now.'.format(count[0])

for i in range(10):
    self_aware_function()
```

* `count` is evaluated at function definition to be a mutable list with one integer element.
* Each invocation of our `self_aware_function` adds another tick to our mutable `count` parameter.

And we have our cheeky, self aware function!
```bash
$ python aware.py
This is my first time doing this
I have done this 2 times now.
I have done this 3 times now.
I have done this 4 times now.
I have done this 5 times now.
I have done this 6 times now.
I have done this 7 times now.
I have done this 8 times now.
I have done this 9 times now.
Who are you to tell me what to do???
```

<span class="note">If you're wondering, mutable default arguments was the answer I gave after a good amount of thought. I believe there are other solutions and I invite you to share them in the comments :)</span>
