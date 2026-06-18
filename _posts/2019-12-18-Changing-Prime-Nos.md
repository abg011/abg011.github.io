---
layout: post
title: "Changing prime numbers"
date: 2019-12-18
tags: [math, graphs]
---

## Shortest path to reach from one prime number to other by changing one digit at a time.
### Problem
Given two four digit prime numbers, suppose 1033 and 8179, we need to find the shortest path from 1033 to 8179 by altering only single digit at a time such that every number that we get after changing a digit is prime. 

For example a solution is 1033, 1733, 3733, 3739, 3779, 8779, 8179.

### Solution
- Find all 4-digit prime numbers using [Sieve of Eratosthenes](https://abg011.github.io/blog/2019/12/17/Sieve-of-Eratosthenes).
- Create a graph.
- Find shortest path using a bfs.

### Code
```
// Sieve of Eratosthenes
vector<int> Sieve(){
	int n=9999;
	vector<bool> prime(n+1, true);
	for(int i=2; i*i<=n; i++){
		if(prime[i]){
			for(int j=i*i; j<=n; j+=i) prime[j] = false;
		}
	}
	vector<int> primes;
	for(int i=1000; i<=n; i++){
		if(prime[i]) primes.push_back(i);
	}
	return primes;
}

// For comparing 2 numbers digit by digit
int compare(int i, int j){
	string num1 = to_string(i);
	string num2 = to_string(j);
	int count = 0;
	for(int i=0; i<4; i++){
		if(num1[i] != num2[i]) count++;
	}
	return count;
}

//Creating graph of prime numbers using Sieve.
map<int, vector<int>> getGraph(){
	vector<int> primes = Sieve();
	int N = primes.size();
	map<int, vector<int>> graph;
	for(int i=0; i<N; i++){
		for(int j=i; j<N; j++){
			if(compare(primes[i], primes[j]) == 1){
				graph[primes[i]].push_back(primes[j]);
				graph[primes[j]].push_back(primes[i]);
			}
		}
	}
	return graph;
}

//BFS to find no of steps
int changePrime(int from, int to){
	map<int, vector<int>> graph = getGraph();
	queue<pair<int, int>> q;
	q.push(make_pair(from, 0));
	while(!q.empty()){
		pair<int, int> front = q.front();
		int index = front.first;
		int level = front.second;
		q.pop();
		if(index == to){
			return level;
		}
		else{
			for(auto x : graph[index]){
				q.push(make_pair(x, level+1));
			}
		}
	}
	return -1;
}
```
### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/shortest-path-reach-one-prime-changing-single-digit-time/)
