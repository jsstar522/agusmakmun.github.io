---
layout: post
title:  "sorted와 int변환의 복잡도"
date:   2020-09-03 10:29:00 +0900
categories: [python]
use_math: true
---

## Sorted와 int변환의 복잡도

* 파이썬에 내장 되어 있는 sorted 함수의 복잡도는 `O(NlogN)` 이다. 꽤나 훌륭한 함수임을 알 수 있다. 

* `int을 string로` 변환하는 것이 `string을 int로` 변환하는 비용이 훨씬 더 높다.

* 변환을 해서 sorting을 해야한다면 sorted의 `key parameter`을 이용하자.

  [(hacker link) Big sorting 문제][https://www.hackerrank.com/challenges/big-sorting/problem?isFullScreen=false]

  이 문제에서 string을 int 규칙에 맞게 정렬해야 하는데, 이런 경우 두번 변환을 하게 되면 비용이 엄청나게 많이 든다. Sorting 할 때만 변환해서 비교해주는 것이 효과적이다. 더 나아가서 int로 변환하지 않고 구별하는 방법도 있다.

  * string의 길이가 길면 일단 큰 수다.
  * string의 길이가 같으면 string의 규칙에 따라 정렬해도 int의 규칙에 따라 정렬한 것과 같다.
  * len(str)은 훨씬 더 적은 비용이 든다.

  ```python
  unsorted = [6, 31415926535897932384626433832795, 1, 3, 10, 3, 5]
  sorted(unsorted, key=lambda x: (len(x), x))
  ```

  







