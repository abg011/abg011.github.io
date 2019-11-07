---
layout: post
title: "Find all four sum numbers(Hashing)"
date: 2019-09-30
---

## Find all four sum numbers
### Problem
#Floyd Warshall Algorithm
The problem is to find shortest distances between every pair of vertices in a given edge weighted directed Graph.
The Graph is represented as Adjancency Matrix, and the Matrix denotes the weight of the edegs (if it exists) else INF (1e7).


### Implementation
First, the algorithm initializes distance using the adjacency matrix adj of the graph:
```
for (int i = 1; i <= n; i++){
  for (int j = 1; j <= n; j++){
    if (i == j) distance[i][j] = 0;
    else if (adj[i][j]) distance[i][j] = adj[i][j];
    else distance[i][j] = INF;
  }
}
```
The time complexity of the algorithm is O ( n 3 ), because it contains three
nested loops that go through the nodes of the graph.
Since the implementation of the Floyd–Warshall algorithm is simple, the
algorithm can be a good choice even if it is only needed to find a single shortest
path in the graph. However, the algorithm can only be used when the graph is so
small that a cubic time complexity is fast enough.
