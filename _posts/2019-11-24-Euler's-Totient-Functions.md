---
layout: post
title: "Euler's Totient Function"
date: 2019-11-24
tags: [math]
---

### Problem
Given a number N, calculate the count of numbers in {1, 2, 3,..., N} that are relatively prime to N.

### Solution

## Euler's Totient Function
- Gives the number of coprime numbers less tha a given number.

![Image](/images/Eulers_Totient_function.png)

Algorithm is as follows-
- Initialize result as N
- For every number from 2 to phi(N)[Totient Function Value], divide N until it indivisible by it ans remove those numbers from result i.e.(result-=result/p).
- If reduced N is >1, remove all the multiples of N from the result too.

##### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/eulers-totient-function/)
