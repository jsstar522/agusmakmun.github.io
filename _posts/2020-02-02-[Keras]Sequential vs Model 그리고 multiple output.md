---
layout: post
title:  "[Keras]Sequential vs Model 그리고 multiple output"
date:   2020-02-02 14:33:00 +0900
categories: [deeplearning]
---

Loss function 2개 이상을 이용해서 학습을 진행하고 싶다면 당연하게도 **output이 2개 이상으로 나와야 한다.** 기본적인 학습에서는 하나의 output을 내놓는 선형적인 모델을 만드는 경우가 대부분이기 때문에 `Sequential`을 사용하는 경우가 많다. 편의성으로 많이 사용하는 `Sequential`과 모델을 더 유연하게 만들 수 있는 `Model`의 차이점에 대해서 알아보자.



## Sequential vs Model

### Sequential

먼저 `Sequential`로 layer를 구성해보자.

```python
from tensorflow.keras.models import Sequential, Model
from tensorflow.keras.layers import Dense

testModel = Sequential()
testModel.add(Dense(5, input_shape=(1,)))	## input_shape을 지정하지 않으면 error를 내뱉는다.
testModel.add(Dense(10))

testModel.summary()
```

`input_shape` 대신에 `input_dim`을 사용할 수 있으며 차원이 있는 경우에는 `input_dim` + `input_length`을 함께 써야 하므로 애초에 `input_shape`을 사용하는게 더 편하다.

> *input_shape=(1, ) 은 input_dim=1과 같다. input_shape은 matrix 개념에서 행이 1개인 matrix를 만들겠다는 것이고, input_dim은 1차원에만 값을 넣겠다는 의미이다. (결국 같은 말이다.)*

```bash
>
Model: "sequential_15"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense_21 (Dense)             (None, 5)                 10        
_________________________________________________________________
dense_22 (Dense)             (None, 10)                60        
=================================================================
Total params: 70
Trainable params: 70
Non-trainable params: 0
_________________________________________________________________
```

**`Sequential`은 `Input` layer를 추가해서 네트워크를 구성하지 않고, 맨 앞 layer의 parameter에서 결정한다.** 결과에서 볼 수 있듯이 하나의 숫자가 들어가서 10개의 숫자를 내뱉는다. 



### Model

이번에는 `Model`을 이용해 layer를 구성해보자.

```python
from tensorflow.keras.models import Sequential, Model
from tensorflow.keras.layers import Input, Dense

inputLayer = Input(shape=(1,), name='input_layer')
hiddenLayer1 = Dense(5, name='hidden_layer1')(inputLayer)
hiddenLayer2 = Dense(10, name='hidden_layer2')(hiddenLayer1)
testModel = Model(inputLayer, hiddenLayer2, name='test_model')
testModel.summary()
```

```bash
>
Model: "test_model"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
input_layer (InputLayer)     [(None, 1)]               0         
_________________________________________________________________
hidden_layer1 (Dense)        (None, 5)                 10        
_________________________________________________________________
hidden_layer2 (Dense)        (None, 10)                60        
=================================================================
Total params: 70
Trainable params: 70
Non-trainable params: 0
_________________________________________________________________
```

`Sequential`과 같이 하나의 값이 들어가서 10개의 값이 나오지만 구성을 보면 `Input Layer`가 맨 처음에 구성되어 있음을 알 수 있다. Parameter 개수는 없기 때문에 **필터느낌으로 생각하면 될 것 같다.**

또 다른 점은 `Sequential`은 **먼저 class를 선언하고** 이후에 연속적으로 layer들을 `add`해 나가는 반면 `Model`은 하나씩 붙여나간 layer를 마지막에 최종적으로 input layer와 정리해 한번에 선언한다. (닭이 먼저냐 달걀이 먼저냐) 그래서 `Sequential`은 위에서부터 아래로 하나씩 추가해나가면 끝나기 때문에 **직관적**이다. 대신 layer들을 선형적으로만 만들어야 하기 때문에 다양하게 활용하기 힘들다. 

예를들어 학습이 완료된 특정 layer를 빼와서 다른 곳에서 활용할 일이 생길 수도 있는데, 이럴 때는 `Model`이 더 적합하다.





## Multiple Output

Output의 개수를 여러개로 사용하고 싶을 때도 `Model`이 더 적합하다. Output을 여러개 만들거나 `Model`에서 두개로 선언하면 되기 때문이다. 

```python
inputLayer = Input(shape=(1,), name='input_layer')
hiddenLayer1 = Dense(5, name='hidden_layer1')(inputLayer)
hiddenLayer2 = Dense(10, name='hidden_layer2')(hiddenLayer1)
testModel = Model(inputLayer, [hiddenLayer2, hiddenLayer2], name='test_model')
testModel.summary()
## 마지막에 output을 두개로 선언하면서 다른 두개의 loss function을 적용할 수 있다.
```

아래와 같이 아예 다른 갈래로 나뉘어져 두개의 output을 만들어낼 수도 있다.

```python
inputLayer = Input(shape=(1,), name='input_layer')
hiddenLayer1 = Dense(5, name='hidden_layer1')(inputLayer)
hiddenLayer2 = Dense(10, name='hidden_layer2')(hiddenLayer1)

hiddenLayer1_1 = Dense(10, name='hidden_layer1_1')(hiddenLayer1)
hiddenLayer1_2 = Dense(20, name='hidden_layer1_2')(hiddenLayer1_1)

testModel = Model(inputLayer, [hiddenLayer2, hiddenLayer1_2], name='test_model')
testModel.summary()
## hiddenLayer1을 통과한 값들은 두개의 갈래로 나뉘어져 두개의 output을 만들어 낸다.
```

