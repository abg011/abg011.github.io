---
layout: post
title: "Maximum Grid Score (LC Hard)"
date: 2026-06-07
---

# LeetCode 3225: Maximum Score From Grid Operations - Intuition and DP Derivation

## Problem Statement

You are given an `n x n` grid of positive integers.

You may perform operations on each column independently. For a chosen column, you can paint any number of cells from the **top** black. Once a cell is painted black, all cells above it in the same column must also be black.

Therefore, after all operations, every column looks like:

```text
B
B
B
W
W
```

where:

* `B` = black cell
* `W` = white cell

For every white cell, if it has a black neighbor immediately to its left or right, its value contributes to the score.

Your goal is to maximize the total score.

---

## Example

Suppose two adjacent columns end up as:

```text
Column A   Column B

B          B
B          B
W          B
W          B
W          W
```

Rows 2 and 3 contain:

```text
W B
W B
```

The white cells in Column A have a black neighbor on the right.

Therefore the score contribution is:

```text
grid[2][A] + grid[3][A]
```

The challenge is to choose the black height of every column to maximize the total score.

---

# Key Insight: Represent Each Column by a Height

Instead of thinking about individual cells, observe that every column is completely determined by:

```text
height = number of black cells from the top
```

For example:

```text
height = 3

B
B
B
W
W
```

Thus the entire grid can be represented as:

```text
h[0], h[1], h[2], ..., h[n-1]
```

where:

```text
0 <= h[i] <= n
```

This transforms the problem from a 2D grid problem into a DP over column heights.

---

# Key Observation

The score is generated only at the boundary between adjacent columns.

Consider two adjacent columns:

```text
prev = 2
curr = 5

Column A   Column B

B          B
B          B
W          B
W          B
W          B
```

Rows 2, 3, and 4 look like:

```text
W B
W B
W B
```

The white cells are in the LEFT column.

Therefore the contribution comes from the previous column:

```text
rows [2,5)
```

---

Now consider:

```text
prev = 5
curr = 2

Column A   Column B

B          B
B          B
B          W
B          W
B          W
```

Rows 2, 3, and 4 look like:

```text
B W
B W
B W
```

The white cells are in the RIGHT column.

Therefore the contribution comes from the current column:

```text
rows [2,5)
```

---

## The Two Pictures To Remember

### Case A: curr <= prev

```text
B B
B B
B W
B W
B W
```

Score comes from:

```text
Current column
rows [curr, prev)
```

---

### Case B: curr > prev

```text
B B
B B
W B
W B
W B
```

Score comes from:

```text
Previous column
rows [prev, curr)
```

Everything in the solution follows from these two pictures.

---

# Prefix Sums

To quickly compute vertical ranges:

```python
col_sum[col][h]
```

stores the sum of the first `h` cells in a column.

Example:

```text
Column

1
4
7
```

Prefix sum:

```text
0
1
5
12
```

Now:

```python
sum(rows [a,b))
=
col_sum[col][b] - col_sum[col][a]
```

can be computed in O(1).

---

# DP State

Define:

```python
dp[i][curr_h][prev_h]
```

Meaning:

> We have processed columns `0...i`.
>
> Column `i` has height `curr_h`.
>
> Column `i-1` has height `prev_h`.

Why do we need two heights?

Because the contribution generated at a boundary depends on both heights:

```text
prev_h <-> curr_h
```

---

# Naive O(n⁴) DP

Suppose:

```text
k       prev_h       curr_h
│          │            │
h[i-2]  h[i-1]       h[i]
```

Then:

```python
dp[i][curr_h][prev_h]
```

can come from:

```python
dp[i-1][prev_h][k]
```

for every possible:

```python
k in [0,n]
```

Transition:

```python
for i:
    for curr_h:
        for prev_h:
            for k:
                dp[i][curr_h][prev_h] = max(...)
```

Complexity:

```text
O(n⁴)
```

Too slow.

---

# Optimizing to O(n³)

The bottleneck is:

```python
max(dp[i-1][prev_h][k])
```

over ranges of `k`.

Instead of recomputing this every time, we precompute:

### Prefix Maximums

```python
prev_max[a][b]
```

Meaning:

```text
Maximum DP value among all heights <= b
```

Think:

```text
Best state from the LEFT side
```

---

### Suffix Maximums

```python
prev_suffix_max[a][b]
```

Meaning:

```text
Maximum DP value among all heights >= b
```

Think:

```text
Best state from the RIGHT side
```

---

# Transition

## Case A

```python
curr_h <= prev_h
```

Picture:

```text
B B
B B
B W
B W
B W
```

Contribution comes from the current column.

```python
gain =
col_sum[i][prev_h]
-
col_sum[i][curr_h]
```

Transition:

```python
dp[i][curr_h][prev_h] =
prev_suffix_max[prev_h][0]
+
gain
```

---

## Case B

```python
curr_h > prev_h
```

Picture:

```text
B B
B B
W B
W B
W B
```

Contribution comes from the previous column.

```python
gain =
col_sum[i-1][curr_h]
-
col_sum[i-1][prev_h]
```

Transition:

```python
dp[i][curr_h][prev_h] =
max(
    prev_suffix_max[prev_h][curr_h],
    prev_max[prev_h][curr_h] + gain
)
```

The prefix and suffix maximum tables eliminate the extra loop over `k`.

Complexity becomes:

```text
O(n³)
```

---

# Why Not Take max(dp[last][*][*])?

This is the trickiest part of the solution.

Not every DP state represents a fully resolved configuration.

Some states still have an "active frontier" that could contribute if another column existed.

Think of:

```text
5 -> 2
```

The taller column is still "hanging down".

The DP keeps track of these partially open configurations.

Therefore:

```python
max(dp[last][*][*])
```

is not necessarily valid.

---

# Virtual Closing Column

To resolve all pending contributions, we imagine one additional column.

Either:

```text
height = 0
```

or

```text
height = n
```

This forces all remaining structures to close.

Therefore the final answer is:

```python
for k in range(n+1):
    ans = max(
        ans,
        dp[n-1][0][k],
        dp[n-1][n][k]
    )
```

Only these states correspond to fully closed configurations.

---

# Complexity

### Time

```text
O(n³)
```

### Space

```text
O(n³)
```

---

# Mental Model

Whenever I revisit this problem, I only remember these two pictures:

### curr <= prev

```text
B B
B B
B W
B W
B W
```

Score comes from the CURRENT column.

---

### curr > prev

```text
B B
B B
W B
W B
W B
```

Score comes from the PREVIOUS column.

Everything else in the solution is simply machinery to efficiently compute the best sequence of heights.
