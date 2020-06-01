---
layout: post
title:  "Reproducible Result(재생산)"
date:   2020-05-19 09:19:00 +0900
categories: [deeplearning]
---

## Reproducible Result(재생산)

학습을 시키다보면 같은 모델을 같은 hyper parameter로 학습시켜도 같은 결과가 나오지 않는다. 그 이유는 weight의 초기화가 random한 값으로 시작하기 때문인데, 복잡하지 않은 모델인 경우에는 학습이 진행되면 특정한 값으로 optimize 되기 쉽지만  복잡한 모델인 경우에는 수많은 parameter (weight)가 있기 때문에 시작 위치가 다르다면 다른 값으로 optimize 될 확률이 있다.



## Seed

Neural netwrok에서는 효과적인 학습을 위해서는 random한 값을 사용한다. **Random variable는 weight 뿐만 아니라, regularization(dropout...), optimization에도 사용된다.** 이 parameter들이 많아지면 많아질수록 random 값에서 나오는 결과가 매번 달라질 수 있다. **이 문제를 해결하기 위한 Seed를 고정하는 방법이 있다.** 

```python
>>> import random
>>> random.random()
0.4288890546751146
>>> random.random()
0.8474337369372327
```

```python
>>> random.seed(1)
>>> random.random()
0.13436424411240122
>>> random.seed(1)
>>> random.random()
0.13436424411240122
```

seed를 특정 수로 지정하고 random 수를 생산하면 같은 값이 만들어지는 것을 알 수 있다. numpy를 이용하여 random 수를 만드는 경우도 있으므로 numpy에도 seed를 고정해보자.

```python
>>> import numpy as np
>>> np.random.random()
0.7203244934421581
>>> np.random.random()
0.00011437481734488664
```

```python
>>> np.random.seed(1)
>>> np.random.random()
0.417022004702574
>>> np.random.seed(1)
>>> np.random.random()
0.417022004702574
```

이제 layer의 weight가 initialize 될 때 random 수가 들어가는 것을 확인해보자.

```python
>>> from tensorflow.keras.models import Sequential
>>> from tensorflow.keras.layers import Dense
>>> model = Sequential()
>>> model.add(Dense(5, input_dim=1))
>>> model.layers[0].get_weights()
[array([[ 0.8726914 ,  0.8746052 , -0.29745555, -0.5940561 ,  0.40158653]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
>>> model = Sequential()
>>> model.add(Dense(5, input_dim=1))
>>> model.layers[0].get_weights()
[array([[-0.8891108 , -0.23939729,  0.48075438,  0.8231933 , -0.4763887 ]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
```

seed를 고정하지 않고 layer를 생성하면 매번 생성할 때마다 weight 값이 다르게 initialize 되는 것을 확인할 수 있다.

```python
>>> import tensorflow as tf
>>> tf.random.set_seed(1)
>>> model = Sequential()
>>> model.add(Dense(5, input_dim=1))
>>> model.layers[0].get_weights()
[array([[ 0.96653724,  0.34232855, -0.13824129, -0.7613833 ,  0.07008076]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
>>> tf.random.set_seed(1)
>>> model = Sequential()
>>> model.add(Dense(5, input_dim=1))
>>> model.layers[0].get_weights()
[array([[ 0.96653724,  0.34232855, -0.13824129, -0.7613833 ,  0.07008076]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
```

seed 번호를 `1` 로 고정하고 layer를 만들어 weight를 확인하면 똑같이 생성된다.



## Weight

실용적인 측면에서 weight 값을 저장하여 모델을 실질적으로 사용가능하게 하려면 weight를 포함한 model을 기록해두는 것이 중요하다. 학습이 잘 된 모델의 weight 값을 나중에 다시 불러와서 사용하면 같은 성능을 보여줄 수 있다.

```python
model = Sequential()
model.add(Dense(5, input_dim=1))

model.save_weights('./your_dir/record_weight')
```

모델에 있는 `save_weights` 로 간편하게 weight를 저장할 수 있다. 이렇게 하면 원하는 폴더에 `h5` 포맷의 파일들이 생기게 된다.

```python
model_2 = Sequential()
model_2.add(Dense(5, input_dim=1))
model_2.load_weights('./your_dir/record_weight')
```

저장한 weight를 모델에 적용시키는 일 또한 간편하다. 실제로 weight 값이 잘 들어가는지 확인해보자.

```python
## model의 weight 저장
>>> from tensorflow.keras.models import Sequential
>>> from tensorflow.keras.layers import Dense
>>> model = Sequential()
>>> model.add(Dense(5, input_dim=1))
>>> model.layers[0].get_weights()
[array([[-0.08190107, -0.5277209 , -0.6905501 ,  0.72204685, -0.8905432 ]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
>>> model.save_weights('test.h5')

## model_2에 model의 weight 적용 (학습이 끝났다고 가정)
## 적용전에는 model의 weight값과 model_2의 weight 값이 다르다
>>> model_2 = Sequential()
>>> model_2.add(Dense(5, input_dim=1))
>>> model_2.layers[0].get_weights()
[array([[0.8351505 , 0.6630027 , 0.22279644, 0.40326595, 0.27649474]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
## load_weights를 적용하면 같아진다
>>> model_2.load_weights('test.h5')
>>> model_2.layers[0].get_weights()
[array([[-0.08190107, -0.5277209 , -0.6905501 ,  0.72204685, -0.8905432 ]],
      dtype=float32), array([0., 0., 0., 0., 0.], dtype=float32)]
```





