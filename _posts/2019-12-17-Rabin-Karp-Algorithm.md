---
layout: post
title: "Rabin-Karp Algorithm"
date: 2019-12-17
tags: [strings]
---
## Rabin-Karp Algorithm for Pattern Searching
### Problem
Given a text txt[0..n-1] and a pattern pat[0..m-1], write a function search(char pat[], char txt[]) that prints all occurrences of pat[] in txt[].

**OR**

Implement strStr - locate a substring (needle) in a string (haystack). Return the first occurence of needle in haystack, or -1 if needle is not part of the haystack.

### Solution
```
# define d 256
# define q 101
int Solution::strStr(const string A, const string B) {
    if(A.size()==0 || B.size()==0 || A.size() < B.size()) return -1;
    int N = A.size();
    int M = B.size();
    
    int h=1, i=0, j=0, p=0, t=0;
    
    for(i=0; i<M-1; i++) h = (h*d)%q;
    
    for(i=0; i<M; i++){
        t = (t*d + A[i])%q;
        p = (p*d + B[i])%q;
    }
    
    for(i=0; i<=N-M; i++){
        if(p==t){
            for(j=0; j<M; j++){
                if(A[i+j]!=B[j]) break;
            }
            if(j==M) return i;
        }
        else{
            t = (d*(t - A[i]*h) + A[i+M])%q;
            if(t<0) t+=q;
        }
    }
    return -1;
}

```

### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/rabin-karp-algorithm-for-pattern-searching/)

**P.S.** 
- Best case and average case complexity **O(n+m)**, while worst case complexity is **O(n x m)**.
- Involves building a hash function to find value of the string in the moving window.
- Algorithm with worst case complexity of **O(n)** - [KMP Algorithm](https://abg011.github.io/blog/2019/12/17/KMP-Algorithm).
