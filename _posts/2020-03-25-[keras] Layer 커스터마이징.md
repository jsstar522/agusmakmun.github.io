---
layout: post
title:  "[keras] Layer 커스터마이징"
date:   2020-03-25 05:40:00 +0900
categories: [machinelearning]
use_math: true
---

Keras에서는 다양한 layer를 기본적으로 제공하고 있다. `Dense`, `Conv2D`, `SimpleRNN`... 하지만 사용하기엔 편할지라도 원하는 기능을 하는 layer를 만들고 싶을 때가 있다. Keras에서는 다음과 같이 layer를 커스터마이징 할 수 있다.

```python
from tensorflow.keras.layers import Layer

class Linear(Layer):
def __init__(self, units=32, input_dim=32):
  super(Linear, self).__init__()
  w_init = tf.random_normal_initializer()
  self.w = tf.Variable(initial_value=w_init(shape=(input_dim, units),
                                            dtype='float32'),
                       trainable=True)
  b_init = tf.zeros_initializer()
  self.b = tf.Variable(initial_value=b_init(shape=(units,),
                                            dtype='float32'),
                       trainable=True)

def call(self, inputs):
  return tf.matmul(inputs, self.w) + self.b

x = tf.ones((2, 2))
linear_layer = Linear(4, 2)
y = linear_layer(x)
print(y)
```

이 내용은 tensorflow 공식 홈페이지에서 제공하고 있는 사용방법이다. 내가 쓸만한 내용으로 더 자세하게 들여다보도록하자.

내가 만들고 싶은 layer는 **10x10 배열이 들어왔을 때, 가운데 2x2 배열의 element들만 모두 0으로 만들고 나머지는 그대로 두고 싶은 layer다.** 이렇게 하고 싶다면, 가운데가 0이고 나머지는 1인 10x10 배열을 input 배열에 각 요소별로 곱해주면 된다. numpy에서의 곱셈은 각 요소들끼리 곱해주는 역할을 한다.(matmul과 다르다!)

```python
import numpy as np
input = np.full((10, 10), 6)
arr = np.ones(shape=(10, 10))
arr[4:6, 4:6] = 0

print(input*arr)
```

위 코드를 실행해보면 알 수 있다.

```text
[[6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 0. 0. 6. 6. 6. 6.]
 [6. 6. 6. 6. 0. 0. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]]
```

현재 우리가 만들고 싶은 layer의 역할이 정확하게 위와 동일하다. 이제 이 과정을 tensor로 적용해주기만 하면 된다.

```python
import tensorflow as tf
from tf.keras.layers import Layer
import numpy as np

class centerZero(Layer):
  def __init__(self, units, input_dim):
    super(centerZero, self).__init__()
    weight = np.ones(shape=(input_dim, units), dtype='float32')
    weight[4:6, 4:6] = 0
    weight = tf.convert_to_tensor(weight)
    self.kernel = tf.Variable(initial_value=weight, trainable=False)
  def call(self, input):
    return input*self.kernel
```

이렇게 간단하게 layer를 만들 수 있다. `centerZero` 클래스 안에서 초기화는 `super()__init__()` 으로 하고, `tf.Variable` 를 통해 layer의 최초 weight 값을 설정할 수 있게 된다. 우리는 가운데가 0인 10x10 배열을 넣어줄 것이므로 `numpy` 를 이용해 먼저 원하는 배열을 만든 뒤, `tf.convert_to_tensor` 를 이용해 배열을 `tensor` 로 바꾸어 주었다. 이 `tensor` 를 최초 weight으로 설정하면 되고 학습이 되지 않도록 설정하기 위해 `trainable=False` 로 설정했다.

마지막으로 `call` 메서드에서 계산을 실행할 수 있다. input 배열과 kernel이 곱해지면 우리가 원하는 배열이 나오기 때문에 둘을 단순곱을 적용시켰다.

이제 layer에 input을 넣고 print 해보면 된다.

```python
input_arr = np.full((10, 10), 6, dtype='float32')
layer = centerZero(10, 10)
## 여기서 10, 10은 위의 __init__에 있는 units과 input_dim으로 전달
layer(input_arr)
```

이제 python 파일을 실행시켜보면 다음과 같이 잘 나오게 된다.

```text
tf.Tensor(
[[6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 0. 0. 6. 6. 6. 6.]
 [6. 6. 6. 6. 0. 0. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]
 [6. 6. 6. 6. 6. 6. 6. 6. 6. 6.]], shape=(10, 10), dtype=float32)
```







