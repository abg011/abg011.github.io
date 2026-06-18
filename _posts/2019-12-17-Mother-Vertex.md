---
layout: post
title: "Finding Mother Vertex in a graph"
date: 2019-12-17
tags: [graphs]
---

## Finding Mother Vertex in a graph
### Problem
**What is a Mother Vertex?**

A mother vertex in a graph G = (V,E) is a vertex v such that all other vertices in G can be reached by a path from v.

**How to find mother vertex?**

_Case 1_:- **Undirected Connected Graph** : In this case, all the vertices are mother vertices as we can reach to all the other nodes in the graph.

_Case 2_:- **Undirected/Directed Disconnected Graph** : In this case, there is no mother vertices as we cannot reach to all the other nodes in the graph.

_Case 3_:- **Directed Connected Graph** : In this case, we have to find a vertex -v in the graph such that we can reach to all the other nodes in the graph through a directed path.

### Solution
- In a DFS, mother vertex if present is visited last. Based on this approach, time complexity - **O(V+E)** as compared to **O(V(V+E))** in naive approach i.e. by DFSing all vertices.

_Code_ - 
```
int findMother(vector<int> graph[], int V){
	vector<bool> visited(V, false);
	int motherI;
	//set last visited vertex as mother vertex
	for(int i=0; i<V; i++){
		if(!visited[i]){
			dfs(graph, i, visited);
			motherI = i;
		}
	}
	//test if it is in fact mother index
	for(int i=0; i<V; i++) visited[i] = false;
	dfs(graph, motherI, visited);
	for(int i=0; i<V; i++){
		if(!visited[i]) return -1;
	}
	return motherI;
}
```
### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/find-a-mother-vertex-in-a-graph/)
