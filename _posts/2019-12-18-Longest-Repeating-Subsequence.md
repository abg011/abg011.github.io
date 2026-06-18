---
layout: post
title: "Longest Repeating Subsequence - DP"
date: 2019-12-18
tags: [dynamic-programming, strings]
---

## Longest Repeating Subsequence  
### Problem
Given a string, print the longest repeating subsequence such that the two subsequence don’t have same string character at same position, i.e., any i’th character in the two subsequences shouldn’t have the same index in the original string.

### Solution
- Modified LCS.
- Apply LCS-type condition only for the indices greater than the index in the first loop. 

### Code
```
#include <bits/stdc++.h>
using namespace std;

int main() {
	//code
	int T;
	cin >> T;
	while(T--){
	    int N;
	    cin >> N;
	    string A;
	    cin >> A;
	    vector<vector<int>> DP(N, vector<int>(N, 0));
      
	    // LCS like loop
	    for(int i=0; i<N-1; i++){
          // j only > i
	        for(int j=i+1; j<N; j++){
	            if(A[i]==A[j]){
	                DP[i][j] = 1 + ((i>0 && j>0) ? DP[i-1][j-1]:0);
	            }
	            else{
	                int x = (i>0) ? DP[i-1][j]:0;
	                int y = (j>0) ? DP[i][j-1]:0;
	                DP[i][j] = max(x, y);
	            }
	        }
	    }
	    
	    cout << DP[N-2][N-1] << endl;
	    
	}
	return 0;
}
```
### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/longest-repeated-subsequence/)
