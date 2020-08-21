---
layout: post
title:  "index, find와 여러개 찾기"
date:   2020-08-19 05:19:12 +0900
categories: [python]
use_math: true
---

## index와 find

list나 string에서 특정 문자나 숫자를 찾는 방법은 `index`나 `find`을 이용하면 된다. 하지만 이 둘의 차이점은 **`index` 은 해당요소를 찾을 수 없을 때 오류**를 발생시키고 **`find` 은 해당요소를 찾을 수 없을 때 -1을 반환**한다.

```python
sample_l = [14, 22, 14, 43, 55]
print(sample_l.index(14))

sample_s = "abcdefabc"
print(sample_s.find('a'))

# return
# 0
# 0
```

하지만 결과를 보면 알 수 있듯이 같은 요소가 있을 경우 맨 앞에 있는 요소밖에 찾지 못한다. 여기서 `numpy` 라이브러리의 `where` 을 활용하면 쉽게 찾을 수 있으나 (첫번째 예시의 경우에 [0, 2]의 형태로 반환된다.) 사용하지 못하는 경우로 가정하고 구현해보자.



## 요소 여러개 찾기

요소를 여러개 찾는 함수는 간단한 for문으로 구현하면 된다. 한 줄이면 끝난다.

```python
sample_l = [14, 22, 14, 43, 55]
search_index = [i for i, value in enumerate(sample_l) if value == 14]
print(search_index)

# return
# [0, 2]
```

