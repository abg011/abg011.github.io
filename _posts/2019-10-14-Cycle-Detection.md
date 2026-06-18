---
layout: post
title: "Cycle Detection"
date: 2019-10-14
tags: [graphs]
---

## Cycle Detection in Directed and Undirected Graphs

### Directed Graph

```
bool dfs(int x, bool visited[], vector<int> adj[], unordered_set<int> uset){
    visited[x] = true;
    uset.insert(x);
    for(auto y: adj[x]){
        if(visited[y]){
            if(uset.find(y) != uset.end()) return true;
        }
        else if(dfs(y, visited, adj, uset)) return true;
    }
    return false;
}

bool isCyclic(int V, vector<int> adj[])
{
    // Your code here
    bool visited[V] = {0};
    for(int i=0; i<V; i++){
        if(!visited[i]){
            unordered_set<int> uset;
            if(dfs(i, visited, adj, uset)) return true;
        }
    }
    return false;
}
```

### Undirected Graph

```
bool dfs(int x, int parent, vector<int> adj[], bool visited[], int V){
    visited[x] = true;
    for(auto y: adj[x]){
        if(!visited[y]){
            if(dfs(y, x, adj, visited, V))
                return true;
        }
        else if(y != parent) return true;
    }
    return false;
}

bool isCyclic(vector<int> adj[], int V)
{
   // Your code here
   bool visited[V] = {0};
   for(int i=0; i<V; i++){
       if(!visited[i]){
           if(dfs(i, -1, adj, visited, V))
                return true;
       }
   }
   return false;
}

```

