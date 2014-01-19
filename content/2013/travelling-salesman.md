Title: Travelling Salesman
Date: 2013-02-01 12:30
Author: Sitong Peng
Tags: algorithms, graph, shortest path
Slug: travelling-salesman
Summary: A discussion about the travelling salesman problem

This is a challenging problem about a travelling salesman that wants to visit a number of cities efficiently. He needs to sell his product in all the cities so he needs to plot out the shortest path to visit all cities but each city only once. I'm not sure why he can't return to any cities but let's assume his tradecraft is in less agreeable goods.

We'll work through the quirks of the travelling salesman problem. The map can be modelled as a [graph](http://en.wikipedia.org/wiki/Graph_theory) where nodes are cities. Also each node is connected to every other node, you can travel in either direction along any edge, and edge lengths are the distances between cities. In other words, you could refer to this graph as complete, undirected, and weighted.

Let's say there are 10 node cities: A to J. The salesman starts at node A.

Finding a "short" path
----------------------
There's a simple way to find a "short" path. I say "short" but this could also lead us to a very long path in certain cases.

1. Starting at A, traverse through each node and make note of which path is the shortest.
2. Take the shortest path (let's say to B) and mark A visited.
3. Repeat step 1 and 2 until all nodes are visited.

When might this be the shortest path? Imagine the cities laid out in a line like so:
```
(A) (B) (C) (D) (E) (F) (G) (H) (I) (J)
```
Using the "short" path algorithm, we can see that we'll just sequentially step left to right and that would certainly find us the shortest path.

When might this be a long path? Try the following:
```
(J)   (A) (B) (C) (D) (E) (F) (G) (H) (I)
```
In this case, we would visit J last, but clearly it would be faster to visit J first, then the rest of the nodes.

The problem with this "short" path algorithm is that we are considering too few paths.

Exhaustive search
-----------------
One way to ensure that we have the shortest path is to simply construct all paths (try all permutations) and pick the shortest. This, however, can be prohibitively expensive as the number of cities increases (see [combinatorial explosion](http://en.wikipedia.org/wiki/Combinatorial_explosion)). For less busy salesmen though, this is a viable option.

Essentially this search is brute force. It can be implemented as breadth first or depth first. They should take the same time since you'll need to search the entire graph in either case. However, usually it is easier to implement depth first search, so we can do that.

1. Starting at A, move to next unvisited node, note distance and mark A as visited.
2. Repeat step 1 until all nodes visited, then you should have some array of all distances travelled. That is your distance path.
3. Now traverse back up your path until you have the option of visiting a different node. Visit that and dig deeper down until you also have that path complete. (in this case, traversing back up the path is done by finishing the innermost loop and moving on to the next node of the immediate outer loop)
4. Eventually you will visit every path and document the distances.

We end up with `(N-1)!` paths which is pretty terrible in terms of computation time (Note this is not `N!` because we are actually only visiting `N-1` nodes since we have defined a start node and we don't return to it). But on the bright side, we know we have the shortest path because we calculated them all!

So what about large N?
----------------------
Well I wish I could give a solid answer here, but this is a heavily studied academic problem that is beyond my current capabilities. I'll leave you with this Stack Overflow [link](http://stackoverflow.com/questions/7159259/optimized-tsp-algorithms) though, which I plan to study a bit further. I'll be sure to share my findings in an upcoming post, so stay tuned.

<span id="note">Shoot me an email or write a comment if you have any suggested material you think I should peruse. I'd love to learn more about this.</span>
