---
layout: post
title: "Submatrices with given sum"
date: 2020-07-23
---

### Problem
Given a matrix, and a target, return the number of non-empty submatrices that sum to target.


### Solution

##### Prerequisite Problem
    Given an array and a traget, find the number of subarrays with sum equal to sum.

##### Solution
_O(n^2) solution_ : Simply compare sum of all subarrays with target.

_O(n) solution_ : Store the current overall sum in a variable, _curr_sum_. Also, store the count of previous _cur_sum(s)_ in a map. At each index in the array, find the value _curr_sum - target_ in the map. Add _map[curr_sum - target]_ to the solution.

