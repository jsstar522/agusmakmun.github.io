---
layout: post
title:  "[Codility] MaxSliceSum"
date:   2020-08-11 10:44:00 +0900
categories: [algorithm]
use_math: true
---

## MaxSliceSum

https://app.codility.com/programmers/lessons/9-maximum_slice_problem/max_slice_sum/

### 문제

```text
A non-empty array A consisting of N integers is given. A pair of integers (P, Q), such that 0 ≤ P ≤ Q < N, is called a slice of array A. The sum of a slice (P, Q) is the total of A[P] + A[P+1] + ... + A[Q].

Write a function:

class Solution { public int solution(int[] A); }

that, given an array A consisting of N integers, returns the maximum sum of any slice of A.

For example, given array A such that:

A[0] = 3  A[1] = 2  A[2] = -6
A[3] = 4  A[4] = 0
the function should return 5 because:

(3, 4) is a slice of A that has sum 4,
(2, 2) is a slice of A that has sum −6,
(0, 1) is a slice of A that has sum 5,
no other slice of A has sum greater than (0, 1).
Write an efficient algorithm for the following assumptions:

N is an integer within the range [1..1,000,000];
each element of array A is an integer within the range [−1,000,000..1,000,000];
the result will be an integer within the range [−2,147,483,648..2,147,483,647].
```



### 풀이

연속된 숫자의 합이 최대일 때를 구하는 것이므로 더하기 빼기의 특징을 알고 있으면 쉽게 풀 수 있다. 음수가 더해지면 숫자는 줄어들기 때문에 앞에서부터 더해지는 값이 음수가 아닌 이상 계속 더하는 것이 이득이다. 즉, 여태까지의 합이 음수가 될 때까지 더하면서 최대값을 저장해두고, 음수가 나오면 새로 더하면 된다. 

```python
def solution(A):
    # slice해서 최대값
    # 음수가 나올때까지 더한다. 음수가 나오면 그 전까지의 합과 음수가 더해져 음수가 되면 새로 더한다
    
    # test case
    # A = [5] * 1000000
    # A = [10, 100, -1, -100, 500]
    # A = [10, 1, -12, 100, 600]
    
    sum_n = 0
    MAX = -2147483648-1
    
    for a in A:
        sum_n += a
        MAX = max(sum_n, MAX)
        if sum_n < 0:
            sum_n = 0
        
    return MAX
```

