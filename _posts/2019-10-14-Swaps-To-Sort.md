---
layout: post
title: "Swaps to sort"
date: 2019-10-15
---

## Calculating the number of swaps to sort an array
### Problem
Given an array of N distinct elementsA[ ]. The task is to find the minimum number of swaps required to sort the array. Your are required to complete the function which returns an integer denoting the minimum number of swaps, required to sort the array.

### Solution
Matching initial and final positions of the elements, form a graph with cycles. No of swaps in each cycle are one less than the no. of edges.

**Editorial** - [here](https://www.geeksforgeeks.org/minimum-number-swaps-required-sort-array/)

**Code** - [here](/codes/swapsort.cpp)
