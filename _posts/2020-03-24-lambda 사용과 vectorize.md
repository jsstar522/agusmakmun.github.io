---
layout: post
title:  "[python]lambda 사용과 vectorize"
date:   2020-03-24 05:07:00 +0900
categories: [algorithm]
use_math: true
---

## Lambda

파이썬은 변수를 저장할 때, `python object`에 값을 할당한다. 덕분에 변수 뒤에 다양한 method를 사용할 수 있는 것이 장점이다. 그래서 다른 언어에 비해 확실히 짧은 코드와 빠른 코딩시간을 갖고 있다. 그 중에 가장 편하다고 느낀 `lambda`에 대해 설명해보고자 한다.

`lambda` 를 통해 간단하게 함수를 만들 수 있다.

어떤 수를 받아서 1을 더해주는 함수를 작성할 때 다음과 같이 만들 수 있다.

```python
def plus_one(x):
  return x+1
```

이는 다음과 같이 `lambda`로 간단하게 표현할 수 있다.

```python
lambda x: x+1
```

**앞에 있는 x는 매개변수를 의미하고 `:` 뒤에는 return이 생략되어 있다고 생각하면 된다.**

이렇게만 만들면 함수가 만들어지고 사용은 다음과 같이할 수 있다.

```python
#방법1
(lambda x: x+1)(2)	#3

#방법2
plus_one = lambda x: x+1
plus_one(2)	#3
```

매개변수를 두개 이상 지정할 수도 있다.

```python
(lambda a, b: a+b)(1,2)	#3
```



## Vectorize

`Vectorize` 는 `numpy` 라이브러리 안에 있는 클래스다. 이 클래스는 특정 함수가 배열의 일부나 전체에 적용될 수 있도록 함수를 변환시킨다. **배열의 여러 원소에 어떤 함수를 적용하고 싶을 때 사용한다.**

다음과 같은 배열이 있다고 하자.

```python
arr = np.array([1,2,3,4,5])
```

3보다 크거나 같은 요소들은 `True`, 작은 요소들은 `False` 로 표시하고 싶다고 하자. (실제로 이러한 조건을  배열을 사용하면 다양한 알고리즘에 적용시킬 수 있다.)

위에서 배운 `lambda` 를 통해 함수를 만들자.

```python
f = lambda x: x >= 3
```

여기서 다음과 같이 실행하면 원하는 값을 얻어낼 수 있다.

```python
f(arr)
# array([False, False,  True,  True,  True], dtype=bool)
```

하지만 두가지 조건이 들어가면 어떨까?

```python
f = lambda x: x>=3 or x<=1
f(arr)
# VALUE ERROR: The truth value of an array with more than one element is ambiguous. Use a.any() or a.all()
```

에러가 발생한다. 배열 요소 중 어느 하나라도 조건을 만족하는 것인지, 모두 만족하는 것인지 알려달라는 뜻이다. 두개의 조건이므로 두개의 배열이 먼저 나온 뒤 `or`가 작동할텐데, 두개의 `or`는 `bool과 bool`을 판단할 수 있지만 배열 사이를 판단할 수 없다. **그래서 우리는 배열 element 하나하나 마다 반복문을 통해 저 두가지 조건(`x>=3 or x<=1`)을 매번 적용시키기 위해 `vectorize` 를 사용할 것이다.**

기본적으로 배열안에 있는 요소들은 `for` 문과 같은 반복문을 통해 모든 요소들에 함수를 취해주어야 하지만, 이를 `vectorize` 를 통해 쉽게 할 수 있다. (`vectorize` 도 사실 반복문을 구현한 클래스이므로 성능이 향상되지는 않는다.)

```python
f = np.vectorize(lambda x: x>=3 or x<=1)
f(arr)
# array([ True, False,  True,  True,  True], dtype=bool)
```

사실은 위에 있는 `f = lambda x: x>=3` 도 배열의 element에 모두 적용되는 함수이므로,  `vectorize` 해주는 것이 맞지만 편의상 잘 쓰지 않는다.