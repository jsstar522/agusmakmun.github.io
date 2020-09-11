---
layout: post
title:  "[백준 11003번] 최소값 찾기"
date:   2020-08-07 11:27:00 +0900
categories: [algorithm]
use_math: true
---

## MaxProfit

https://app.codility.com/programmers/lessons/9-maximum_slice_problem/max_profit/

### 문제

```text
An array A consisting of N integers is given. It contains daily prices of a stock share for a period of N consecutive days. If a single share was bought on day P and sold on day Q, where 0 ≤ P ≤ Q < N, then the profit of such transaction is equal to A[Q] − A[P], provided that A[Q] ≥ A[P]. Otherwise, the transaction brings loss of A[P] − A[Q].

For example, consider the following array A consisting of six elements such that:

  A[0] = 23171
  A[1] = 21011
  A[2] = 21123
  A[3] = 21366
  A[4] = 21013
  A[5] = 21367
If a share was bought on day 0 and sold on day 2, a loss of 2048 would occur because A[2] − A[0] = 21123 − 23171 = −2048. If a share was bought on day 4 and sold on day 5, a profit of 354 would occur because A[5] − A[4] = 21367 − 21013 = 354. Maximum possible profit was 356. It would occur if a share was bought on day 1 and sold on day 5.

Write a function,

class Solution { public int solution(int[] A); }

that, given an array A consisting of N integers containing daily prices of a stock share for a period of N consecutive days, returns the maximum possible profit from one transaction during this period. The function should return 0 if it was impossible to gain any profit.

For example, given array A consisting of six elements such that:

  A[0] = 23171
  A[1] = 21011
  A[2] = 21123
  A[3] = 21366
  A[4] = 21013
  A[5] = 21367
the function should return 356, as explained above.

Write an efficient algorithm for the following assumptions:

* N is an integer within the range [0..400,000];
* each element of array A is an integer within the range [0..200,000].
```



### 풀이

주식을 사고 팔았을 때 가장 큰 이득을 뽑아내는 문제다. 주식은 먼저 사고 나중에 팔아야 한다. 즉 검색하면서 최소값이었던 주가를 갱신하면서 차이값이 가장 큰 것을 파악하면 된다.

```python
def solution(A):
    
    MIN = 200000
    MAX_diff = 0
    for a in A:
        if a < MIN:
            MIN = a
        
        if a - MIN > MAX_diff:
            MAX_diff = a - MIN
            
    return MAX_diff
```

