Title: Understanding heaps
Date: 2013-02-02 23:59
Author: Sitong Peng
Tags: heaps, data structures, python
Slug: understanding-heaps
Summary: Walking through the implementation of a heap and its insert/extract min methods

I'm spending this weekend filling in some of the holes in my programming background, data structures in particular. As part of the learning process, I want to share my new understanding of heaps.

What is it?
-----------
A heap is a type of [priority queue](http://en.wikipedia.org/wiki/Priority_queue), which is just a queue where dequeued elements are primarily selected based on their "priority" and secondarily selected based on the "first in first out" principle. A heap prioritizes by key values of objects it stores, which leads to the two flavors of heaps: the max heap and min heap. The max heap will dequeue the objects with the highest keys first. If there are multiple maxes, the heap will dequeue by order of insertion. As you'd expect, the min heap is the same except it dequeues objects with the lowest keys first.

Introducing the max heap
------------------------
Although a heap is technically a priority queue, and although we are going to implement it as an array, heaps are commonly represented as a binary tree. Here is the max heap we're going to work with:

![Binary max heap](http://upload.wikimedia.org/wikipedia/commons/thumb/1/1c/Heap_delete_step0.svg/500px-Heap_delete_step0.svg.png)

There are two conditions that must be met for a binary tree to be a max heap.

1. Every parent node must be at least as large as either child nodes. This means the max key will always be at the top of the tree.
2. The tree must be complete at all levels except the deepest. The deepest level must be populated from the left. This means if the above tree had another node, it would be the left child of the node 8.

We're going to implement this heap as an array. This boils down to extracting nodes left to right, top to bottom. Here's what it will look like:
```python
[11, 5, 8, 3, 4]
```
<span id="note">Normally heaps hold objects with the keys we are showing in the tree and array representation. For simplicity, we'll just assume the objects and keys are one and the same.</span>

We also need to keep track of the parent and children of any given node. That's pretty natural for binary trees but how is that implemented in arrays? Turns out it's pretty simple with one-indexing, due to the way we are pulling elements off the tree.
```
  1  2  3  4  5  <-- One-indexing
[11, 5, 8, 3, 4]
                             EXAMPLE:
parent(i) = floor(i/2)            parent(2) = 1
left_child(i) = i*2           left_child(2) = 4
right_child(i) = i*2 + 1     right_child(2) = 5
```
Let's code up what we have. I prefer to have zero-indexing so the math on the parent/children calculation will have to compensate.
```python
class MaxHeap(object):
    def __init__(self):
        self.arr = []

    def parent(self, i):
        if i == 0:
            return 0
        return (i + 1)//2 - 1

    def children(self, i):
        left = (i + 1) * 2 - 1
        right = left + 1
        return left, right
```
Now, a heap has two basic operations: insert and extract_max (extract_min). These are guaranteed to run in `O(logN)` time, or in other words, the height of the binary tree.

Insert
------
What does an insert look like in our binary tree model? Following the conditions of a binary heap, we have to insert the new object at the deepest level, moving in from the left. That corresponds to the node marked X below:

![Insert at X](http://upload.wikimedia.org/wikipedia/commons/thumb/a/ac/Heap_add_step1.svg/500px-Heap_add_step1.svg.png)

It should be pretty obvious that the object we insert could break the heap property of this tree. Whatever key we end up with, we first need to compare it with its parent node. The parent node's key is 8, so if we insert an object with `key <= 8` then we wouldn't have to do anything further. But what if the key is something higher like 15? Then our insertion would break heap condition #2.

In this situation, the next step is to swap our inserted node with it's parent node. 

![Swap with parent](http://upload.wikimedia.org/wikipedia/commons/thumb/1/16/Heap_add_step2.svg/500px-Heap_add_step2.svg.png)

Cool. But now when we check 15 against its new parent, the root, we notice we're still in trouble. So we swap again. These swap acts are also known as "bubbling up" or "heapifying up", as the inserted object is promoted levels due to its priority.

![Promoted to root](http://upload.wikimedia.org/wikipedia/commons/thumb/5/51/Heap_add_step3.svg/500px-Heap_add_step3.svg.png)

Now that 15 has made it's way up to the root node, we can stop. As you can see, the new tree follows all conditions to be classified as a max heap. Here's the code for the insert operation.
```python
    def insert(self, key):
        self.arr.append(key)
        i = len(self.arr) - 1
        parent = self.parent(i)
        while self.arr[i] > self.arr[parent]:
            self.arr[i], self.arr[parent] = self.arr[parent], self.arr[i]
            if parent:
                i = parent
                parent = self.parent(i)
            else: # parent is root
                break
```
As you can see, we first insert to the end of the array, then while the inserted key is larger than its parent, we swap it upward until it finds its place.

Extract Max
-----------
I believe this is also known as Delete Max, but in either case, this operation removes the max from the heap. And because we're working with a heap, we know the max is the root of the tree (or first element of the array), so we certainly don't need to traverse through the entire structure to determine it.

![Binary max heap](http://upload.wikimedia.org/wikipedia/commons/thumb/1/1c/Heap_delete_step0.svg/500px-Heap_delete_step0.svg.png)

Now, as mentioned before, the first step is to remove the root node. That leaves us with a tricky situation. What node will replace the root?

Let's go through our options.

* Pull up the 5. Well, now we fail both conditions. The root is now less than the right child, and more importantly we have an incomplete binary tree.
* Pull up the 8. Cool, our root is the max, but now we have an entire empty branch on the right. That seems like a big problem and we can't just fix it by pulling from an entirely separate branch.
* Pull up the 3. Breaks the complete tree again.
* Pull up the 4. We still have a complete tree (in terms of a heap at least). We have a bad root but maybe we can shift that node back down.

So the general solution seems to be, promote the last element of the tree/array.

![Big promotion here](http://upload.wikimedia.org/wikipedia/commons/thumb/e/ee/Heap_remove_step1.svg/500px-Heap_remove_step1.svg.png)

So now the new root is no longer the max. We need to swap it with one of its children. If we swap with 5, we'll still have the same problem. We'll need to swap with 8. This should lead us to the realization that we'll always want to swap with the higher of the two children, otherwise we'll just be making more of a mess of things.

![Back to normalcy](http://upload.wikimedia.org/wikipedia/commons/thumb/2/22/Heap_remove_step2.svg/500px-Heap_remove_step2.svg.png)

Now we have our max extracted heap!

Here's the code:
```python
    def extract_max(self):
        self.arr[0] = self.arr[-1]
        del self.arr[-1]
        i = 0
        left, right = self.children(i)
        while right < len(self.arr): # if right exists, so does left
            if self.arr[i] < self.arr[left] or self.arr[i] < self.arr[right]:
                if self.arr[left] > self.arr[right]:
                    self.arr[left], self.arr[i] = self.arr[i], self.arr[left]
                    i = left
                else:
                    self.arr[right], self.arr[i] = self.arr[i], self.arr[right]
                    i = right
                left, right = self.children(i)
            else: # we have a heap again
                return
        if left < len(self.arr):
            if self.arr[left] < self.arr[i]:
                self.arr[left], self.arr[i] = self.arr[i], self.arr[left]
```
<span id="note">I'm not actually returning the max here, but you can build that in pretty easily.</span>

`extract_max` is a bit more complicated than `insert`. Swapping the new root down takes a few more comparision steps as we have to decide which child to swap with. We're still within `O(logN)` time though, since we will never need to swap more than the height of the binary tree.

So what are heaps good for?
---------------------------
In general, when you constantly need to access the min or max of some data, heaps are a logical choice, as you just need to pluck it from the top of the tree/front of the array and do a little rearranging. This means they are useful for tasks like managing bandwith on a router (always send the prioritized traffic first) or handling asynchronous event processing (firing off shortest tasks first).

Heaps are also useful in speeding up certain algorithms that require multiple min or max computations. One example is Dijkstra's shortest path algorithm, where you are constantly computing the minimum path for each node.

So, hopefully you've now got a solid understanding of heaps. Next time you're working on something and you find yourself repeatedly taking minimums or maximums, you should give them a try. They will make your life easier.
 
