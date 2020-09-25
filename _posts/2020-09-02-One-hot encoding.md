---
layout: post
title:  "One-hot encoding"
date:   2020-09-02 10:02:00 +0900
categories: [machinelearning]
use_math: true
---

## One-hot Encoding

자연어 처리에서 가장 먼저 해야할 일은 문자를 숫자로 바꾸는 일이다. 기계는 문자가 아닌 숫자를 이해하기 때문에 모든 문자를 숫자로 바꿔야한다. 음성 데이터나 이미지 데이터는 숫자로 이루어져 있지만 텍스트는 그렇지 않기 때문에 자연어 처리 (NLP)에서 encoding을 해주는 일이 중요하다. Encoding을 하는 작업 중에서 가장 쉬운 방식이 `one-hot encoding`이다. **이 방식은 N개의 단어가 있을 때, 0과 1로 이루어진 크기 N의 벡터로 바꾸는 방식이다.**

```
안녕, 나는 강아지야. 종류는 골든 리트리버야.
```

다음과 같은 문장을 one-hot encoding 할 때, 문장을,

```python
['안녕', '나', '는', '강아지', '야', '종류', '는', '골든', '리트리버', '야']
```

로 먼저 단어를 나눌 수 있다. 이 작업을 `토큰화`라고 하고 언어나 방식에 따라 다르게 수행될 수 있다. 강아지라는 단어를 의미하는 벡터는 다음과 같다.

```python
[0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
```

이 방식은 성능도 좋고 간단하게 사용할 수 있지만 **단어의 연관성을 전혀 추측할 수 없다는 단점**이 있다. 게다가 단어가 많은 경우 벡터의 크기가 무한대로 늘어날 수 있다는 단점도 있다.

One-hot encoding은 다양한 프레임워크에서 라이브러리로 제공하고 있다. 케라스로 one-hot encoding을 해보자.

```python
text="안녕, 나는 강아지야. 종류는 골든 리트리버야."
```

문장을 단어로 나누는 `토큰화`를 먼저 해야한다.

```python
from tensorflow.keras.preprocessing.text import Tokenizer
t = Tokenizer()
t.fit_on_texts([text])
print(t.word_index)
```

```python
{'안녕': 1, '나는': 2, '강아지야': 3, '종류는': 4, '골든': 5, '리트리버야': 6}
```

케라스 안에 있는 Tokenizer을 이용하니 위와 같이 단어가 쪼개졌다. (사용하는 것마다 모두 다르게 쪼개진다.) 문장을 정수 시퀀스로 만들어줄 수도 있다.

```python
encoded = t.texts_to_sequences([text])[0]
print(encoded)

# [1, 2, 3, 4, 5, 6]
```

이제 one-hot encoding을 해보자.

```python
from tensorflow.keras.utils import to_categorical
one_hot = to_categorical(encoded)
print(one_hot)
```

```python
[[0. 1. 0. 0. 0. 0. 0.]
 [0. 0. 1. 0. 0. 0. 0.]
 [0. 0. 0. 1. 0. 0. 0.]
 [0. 0. 0. 0. 1. 0. 0.]
 [0. 0. 0. 0. 0. 1. 0.]
 [0. 0. 0. 0. 0. 0. 1.]]
```

1부터 6까지 정수를 0과 1로만 이루어진 벡터로 표현했다.

