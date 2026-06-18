---
layout: post
title: "KMP Algorithm"
date: 2019-12-17
tags: [strings]
---
## KMP Algorithm for Pattern Searching
### Problem
Given a text txt[0..n-1] and a pattern pat[0..m-1], write a function search(char pat[], char txt[]) that prints all occurrences of pat[] in txt[].

**OR**

Implement strStr - locate a substring (needle) in a string (haystack). Return the first occurence of needle in haystack, or -1 if needle is not part of the haystack.

### Solution
```
void LPS(const string B, vector<int> &lps){
    lps[0]=0;
    int i=1, len=0;
    while(i<B.size()){
        if(B[i]==B[len]){
            len++;
            lps[i]=len;
            i++;
        }
        else{
            if(len!=0){
                len = lps[len-1];
            }
            else{
                lps[i]=0;
                i++;
            }
        }
    }
}
int Solution::strStr(const string A, const string B) {
    if(A.size()==0 || B.size()==0 || B.size() > A.size()) return -1;
    vector<int> lps(B.size());
    LPS(B, lps);
    
    int i=0, j=0;
    while(i<A.size()){
        if(A[i]==B[j]){
            i++;
            j++;
        }
        if(j==B.size()){
            return i-j;
        }
        else if(i<A.size() && A[i]!=B[j]){
            if(j!=0) j=lps[j-1];
            else i++;
        }
    }
    return -1;
}

```
### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/kmp-algorithm-for-pattern-searching/)
### P.S.
- Naive algorithm takes **O(m x n)** time in worst case whereas for KMP worst case complexity is **O(n)**.
- Function [LPS](https://abg011.github.io/blog/2019/12/16/Longest-prefix-suffix) is used to find longest prefix that is also a suffix for the pattern. 
- Another algorithm for pattern searching [Rabin-Karp Algorithm](https://abg011.github.io/blog/2019/12/17/Rabin-Karp-Algorithm)
