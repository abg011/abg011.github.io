---
layout: post
title: "Longest Prefix Suffix"
date: 2019-12-16
tags: [strings]
---
## Longest Prefix Suffix
### Problem
Given a string of character, find the length of longest proper prefix which is also a proper suffix.
Example:
S = abab
lps is 2 because, ab.. is prefix and ..ab is also a suffix.

### Example:
#### Input:
2
abab
aaaa

#### Output:
2
3


### Solution
```
#include <bits/stdc++.h>
using namespace std;

int main() {
	//code
	int T;
	cin >> T;
	while(T--){
	    string s;
	    cin >> s;
	    vector<int> lps(s.size(), 0);
	    lps[0] = 0;
	    int len=0;
	    int i=1;
	    while(i<s.size()){
	        if(s[i]==s[len]){
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
	    cout << lps[s.size()-1]<<endl;
	}
	return 0;
}
```
### P.S. 
This is the preprocessing step of the KMP algorithm for finding pattern in a string/text.

#### Time Complexity - O(n)

#### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/kmp-algorithm-for-pattern-searching/)
