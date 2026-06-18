---
layout: post
title: "Maximum area rectangle in a Histogram"
date: 2019-12-19
tags: [arrays, stack]
---

## Maximum area rectangle in a histogram  
### Problem 
Find the largest rectangular area possible in a given histogram where the largest rectangle can be made of a number of contiguous bars. For simplicity, assume that all bars have same width and the width is 1 unit.

### Solution
- Linear time solution using a stack. 

- For each element find the first lesser element in the right(rless) and in the left(lless).

- Initial value of rless and lless for each entry is -1 and N respectively.

- For each element, pop elements from the stack greater than current. Current element becomes 'rless' for all those.

- The first element in the stack lesser thn current is 'lless' for current. Push current. 

- At the end, pop all the elements from the stack and set their 'rless' value to N.

### Code
```
int maxAreaRectangle(vector<int> &A){
    int N=A.size();
    vector<int> lless(N, -1);
    vector<int> rless(N, N);
    int ans=0;
    stack<int> s;
    //s.push(-1);
    for(int i=0; i<N;i++){
        while(!s.empty() && A[s.top()]>=A[i]){
            rless[s.top()]=i;
            s.pop();
        }
        if(!s.empty()){
            lless[i]=s.top();
        }
        s.push(i);
    }
    while(!s.empty()){
        rless[s.top()]=N;
        s.pop();
    }
    
    for(int i=0; i<N; i++){
        int max = A[i]*(rless[i]-lless[i]-1);
        ans=(max>ans)?max:ans;
    }
    return ans;
}
```
### P.S. -
- Used in 'calculating maximum area rectangle in a matrix'
- Complexity - **O(N)**

### Reference - [geeksforgeeks](https://www.geeksforgeeks.org/largest-rectangle-under-histogram/)
