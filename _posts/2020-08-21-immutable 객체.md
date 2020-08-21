---
layout: post
title:  "mutable과 immutable 객체"
date:   2020-03-21 08:13:00 +0900
categories: [python]
use_math: true
---

## 파이썬의 immutable 객체

파이썬의 객체 (자료구조라고 생각하면 되나, 파이썬에서는 모든 값이 객체이다.)은 mutable과 immutable의 성질을 가진 두가지 종류로 나뉜다. Mutable 성질을 가진 객체는 `list, dictionary, numpy` 등이 있고, immutable 성질을 가진 객체는 `number, string, tuple` 등이 있다. 이 성질은 복사하고 복사된 값을 변경할 때 차이점을 확인 할 수 있다.

> Mutable - list, dictionary, ndarray
>
> Immutable: number, string, tuple



### Mutable

먼저 mutable을 확인해보기 위해 list을 만들어보자. 그리고 새로운 list에 복사시켜보자.

```python
>>> test_list = [1, 2, 3]
>>> new_list = test_list
>>> new_list
[1, 2, 3]
```

그리고 new_list의 두번째 값을 4로 변경시키고 `test_list`와 `new_list`을 확인해보자.

```python
>>> new_list[1] = 4
>>> new_list
[1, 4, 3]
>>> test_list
[1, 4, 3]
```

`new_list` 의 두번째 값은 당연하게도 교체되지만 `test_list` 의 두번째 값도 교체되는 것을 확인할 수 있다. Mutable 객체는 값을 복사시키는게 아닌 `참조변수` 의 주소값을 복사하기 때문이다.

```python
# 주소가 동일하다
>>> id(test_list)
4302303456
>>> id(new_list)
4302303456
```



### 복사하는 법

주소가 완전히 다른 별개의 변수로 복사하고 싶다면 다음과 같이 할 수 있다.

```python
>>> test_list = [1,2,3]
>>> new_list = test_list[:]
>>> id(test_list)
4337154096
>>> id(new_list)
4337154024
```

```python
>>> test_list = [1,2,3]
>>> new_list = list(test_list)
>>> id(test_list)
4337154240
>>> id(new_list)
4337154600
```

그런데, **2차원 이상의 배열일 경우 위와 같은 방법이 먹히지 않는다.**

```python
>>> test_list = [[1,2,3], [4,5,6], [7,8,9]]
>>> new_list = list(test_list)
>>> new_list[0][0] = 2
>>> new_list
[[2, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> test_list
[[2, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> id(test_list[0])
4337154600
>>> id(new_list[0])
4337154600
```

이때는 `copy 라이브러리` 를 사용해야한다. copy의 deepcopy을 이용하면 모든 list가 각각 다른 주소값을 갖게 된다.

```python
>>> import copy
>>> test_list = [[1,2,3], [4,5,6], [7,8,9]]
>>> new_list = copy.deepcopy(test_list)
>>> new_list[0][0] = 2
>>> new_list
[[2, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> test_list
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> id(test_list[0])
4337154384
>>> id(new_list[0])
4337733784
```



