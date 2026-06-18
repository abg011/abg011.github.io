---
layout: post
title: "Circle of Strings - Eulerian Circuit"
date: 2019-11-07
tags: [strings, graphs]
---

##  Circle of strings
### Problem
Given an array of strings A[], determine if the strings can be chained together to form a circle. A string X can be chained together with another string Y if the last character of X is same as first character of Y. If every string of the array can be chained, it will form a circle.

For eg for the array arr[] = {"for", "geek", "rig", "kaf"} the answer will be Yes as the given strings can be chained as "for", "rig", "geek" and "kaf"

### Eulerian Circuit
A (un)directed graph contains a Eulerian Cycle if all the edgefs belong to the same connected component.

Essesntially, we need to find if there is only one strongly connected component in the graph. Each edge in this case is between first and last character of each string.

### Solution
Code(using Kosaraju's Algorithm) - [here](/codes/stringCircle.cpp)

Editorial - [GeeksforGeeks](https://www.geeksforgeeks.org/given-array-strings-find-strings-can-chained-form-circle/)

Practice - [GeeksforGeeks](https://practice.geeksforgeeks.org/problems/circle-of-strings/0)
