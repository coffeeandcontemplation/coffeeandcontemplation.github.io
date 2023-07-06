---
layout: post
title: Dijkstra's algorithm
comments: true
tags: [dijkstra, algorithm]
---

Dijkstra's algorithm is a greedy algorithm to solve the single-source shortest path problem on a weighted directed graph in which edge weights are non-negative.
The single-source shortest path problem is to find the shortest paths to every node in a graph from a single node.

We can see what makes this greedy in the course of this article.

The below diagram shows the input to and the output from dijkstra's algorithm. The edges are weighted and directed. The shortest paths to each node is shown in red in the second diagram

We'll code in python a variation of dijkstra to solve a single-pair shortest path problem. We'll only find the shortest path between a source and a destination.

First, let us represent the graph. We can use the adjacency list representation for this.
A simple dict is enough. If there is an edge from `S -> A` with a aweight of 4, the dict would have this:

```
{ "S": [("A", 4)]
```

The python code to create and initialize the graph is below

<!-- TODO Add stuff here -->

```python
```

<!-- Todo -->

\[\] What is greedy algorithm
\[\] How is the time complexity calculated
\[\] How could you find shortest path without dijkstra
\[\] How to optimize dijkstra
