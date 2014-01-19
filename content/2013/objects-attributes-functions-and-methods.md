Title: Objects, attributes, functions, and methods
Date: 2013-04-07 16:00
Author: Sitong Peng
Tags: objects, python, functions
Slug: objects-attributes-functions-and-methods
Summary: Discussing python objects, attributes, and the difference between functions and methods

I set some time out to open the box on some [Python](http://www.python.org) fundamentals this weekend. I have a few topics to write about but, for today, let's talk a little about objects.

Objects
-------
Objects are a big deal in Python. And rightfully so, as most developers use Python in an object oriented manner, though you can certainly write imperatively or pseudo-functionally too. In fact, some people subscribe to the thought that [everything in Python is an object](http://www.diveintopython.net/getting_to_know_python/everything_is_an_object.html). I'm not going to argue about whether that's true or not so we'll just tip-toe around it and state that there are an **awful lot of objects**.

So what is an object? Let's use [Wikipedia](http://en.wikipedia.org/wiki/Object_(computer_science)#Properties_of_an_object)'s definition. Objects are characterized by these properties:

#### Identity (Objects are unique from each other)
```python
class Trivial(object):
    pass

obj, diff_obj = Trivial(), Trivial()

id(obj) # 25851216
id(diff_obj) # 26133840
```

#### State (Can store data in object)
```python
import os # modules are objects
os.my_data = 'arbitrary'
print os.my_data # arbitrary
```

#### Behavior (Can manipulate the object)
```python
print ' '.join(['strings', 'are', 'objects', 'with', 'methods'])
# strings are objects with methods
```

Attributes and Methods
----------------------
You're probably familiar with setting object attributes to static data or writing simple methods if you've worked with objects in the past.
```python
class Lannister(object):
    def __init__(self, name):
        self.name = name

    def say_family_motto(self):
        print '{} says: "Hear Me Roar!"'.format(self.name)

tyrion = Lannister()

print tyrion.name
# Tyrion Lannister
tyrion.say_family_motto()
# Tyrion Lannister says: "Hear Me Roar!"
```
But you're actually not limited to such trivial behavior. For instance, since functions are first class objects in Python, you could easily do the following too.
```python
def say_common_motto():
    print 'A Lannister Always Pays His Debts.'

tyrion.say_common_motto = say_common_motto

tyrion.say_common_motto()
# A Lannister Always Pays His Debts.
```
As you can see, we've set the `.say_common_motto` attribute to the function object `say_common_motto`, then invoked the attribute.

Before we continue, we should make a distinction between `.say_common_motto` and `.say_family_motto`.
```python
type(tyrion.say_common_motto)
# <type 'function'>
type(tyrion.say_family_motto)
# <type 'instancemethod'>
```
Python resolves the type of `.say_common_motto` to a function object and `.say_family_motto` to an instance method object. What's the difference?

You can think of a method as a function with a unique difference. A method always takes the object it's associated with as it's first argument. You don't have a choice on the matter.

Under the hood, when Python sees `tyrion.say_family_motto()`, it first looks for a `.say_family_motto` attribute in `tyrion` (this means you can override an object method by writing to an object attribute with the same name). When it doesn't find that, the interpreter will jump up to the class level and look for `say_family_motto` function defined at the class level. Once found, it will invoke the function as a method - namely, by attaching the `tyrion` object to the head of the argument list. That's also why a method definition always includes a positional argument placeholder first (traditionally called `self` in Python).

Methods are really just a shortcut though. You can actually just define your own function with an argument placeholder for an object and attach the function to the object attribute. An example makes this clearer. Suppose we rewrote the `say_common_motto` function to make it equivalent to a method.
```python
def say_common_motto(self):
    print '{} says: "A Lannister Always Pays His Debts."'.format(self.name)

tyrion.say_common_motto = say_common_motto

tyrion.say_common_motto(tyrion)
# Tyrion Lannister says: "A Lannister Always Pays His Debts."

tyrion.say_family_motto()
# Tyrion Lannister says: "Hear Me Roar!"
```
As you can see, defining a method in the class is a lot cleaner than attaching a function to the instance object. But both styles get you to the same resolution eventually. 

Anyways, hopefully you know a little more about Python objects now.
