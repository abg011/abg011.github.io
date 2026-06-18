---
layout: post
title: "Sieve of Eratosthenes"
date: 2019-12-17
tags: [math]
---

## Sieve of Eratosthenes
### Problem
Given a number n, print all primes smaller than or equal to n. It is also given that n is a small number.

The sieve of Eratosthenes is one of the most efficient ways to find all primes smaller than n when n is smaller than 10 million or so.

### Solution
Following is the algorithm to find all the prime numbers less than or equal to a given integer n by Eratosthenes’ method:

- Create a list of consecutive integers from 2 to n: (2, 3, 4, …, n).
- Initially, let p equal 2, the first prime number.
- Starting from p2, count up in increments of p and mark each of these numbers greater than or equal to p2 itself in the list. These numbers will be p(p+1), p(p+2), p(p+3), etc..
- Find the first number greater than p in the list that is not marked. If there was no such number, stop. Otherwise, let p now equal this number (which is the next prime), and repeat from step 3.

```
void SieveOfEratosthenes(int n) 
{ 
    // Create a boolean array "prime[0..n]" and initialize 
    // all entries it as true. A value in prime[i] will 
    // finally be false if i is Not a prime, else true. 
    bool prime[n+1]; 
    memset(prime, true, sizeof(prime)); 
  
    for (int p=2; p*p<=n; p++) 
    { 
        // If prime[p] is not changed, then it is a prime 
        if (prime[p] == true) 
        { 
            // Update all multiples of p greater than or  
            // equal to the square of it 
            // numbers which are multiple of p and are 
            // less than p^2 are already been marked.  
            for (int i=p*p; i<=n; i += p) 
                prime[i] = false; 
        } 
    } 
  
    // Print all prime numbers 
    for (int p=2; p<=n; p++) 
       if (prime[p]) 
          cout << p << " "; 
} 
```
### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/sieve-of-eratosthenes/)
