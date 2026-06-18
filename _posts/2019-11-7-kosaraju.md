---
layout: post
title: "Kosaraju's Algorithm for Strongly Connected Components"
date: 2019-11-07
tags: [graphs]
---

## Strongly Connected Components using Kosaraju's Algorithm
### Problem
Given a graph with N nodes and M directed edges. Your task is to complete the function kosaraju() which returns an integer denoting the number of strongly connected components in the graph.

### Solution
Need 2 DFS, dfs1 and dfs2.
```
void dfs1(int index, stack<int> &s, bool visited[], vector<int> adj[]){
    visited[index] = true;
    for(auto a: adj[index]){
        if(!visited[a]) dfs1(a, s, visited, adj);
    }
    s.push(index);
}

void dfs2(int index, bool visited[], vector<int> adj[]){
    visited[index] = true;
    for(int a: adj[index]){
        if(!visited[a]) dfs2(a, visited, adj);
    }
}

int kosaraju(int V, vector<int> adj[])
{
    // Your code here
    stack<int> s;
    bool visited[V] = {0};
    
    
    //fill the stack - DFS1
    for(int i=0; i<V; i++){
        if(!visited[i]){
            dfs1(i, s, visited, adj);
        }
    }
    
    //graph transpose
    vector<int> adjPrime[V];
    for(int i=0; i<V; i++){
        for(auto a : adj[i]){
            adjPrime[a].push_back(i);
        }
    }
    
    //Empty the stack - DFS2
    for(int i=0; i<V; i++) visited[i] = false;
    int count = 0;
    while(!s.empty()){
        int t = s.top();
        //cout << t << endl;
        s.pop();
        if(!visited[t]){
            dfs2(t, visited, adjPrime);
            count++;
        }
    }
    
    return count;
}

```
