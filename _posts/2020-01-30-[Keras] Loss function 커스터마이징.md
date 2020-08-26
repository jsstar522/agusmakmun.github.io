---
layout: post
title:  "[Keras] Loss function 커스터마이징"
date:   2020-01-30 07:35:23 +0900
categories: [deeplearning]
---

다양한 문제를 다루다 보면 기존에 존재하는 loss function 만으로 학습이 잘 안되는 경우가 있다. 완전히 새로운 function을 만들어내지 못하더라도 기존에 있던 function에서 bias를 넣어주면 학습이 더 잘 이뤄질 수 있다.



## y_true, y_pred

keras에서 모델을 compile하기 위해서는 다음과 같이 수행한다.

```python
model.compile(loss='binary_crossentropy', optimizer='sgd', metrics=['accuracy'])
```

`keras documentation`을 보면 다음과 같이 작성되어 있다.



>*You can either pass the name of an existing loss function, or pass a TensorFlow/Theano symbolic function that returns a scalar for each data-point and takes the following two arguments:*
>
>**y_true**: True labels. TensorFlow/Theano tensor.
>
>**y_pred**: Predictions. TensorFlow/Theano tensor of the same shape as y_true.



`y_true`와 `y_pred`을 이용해서 scalar 값을 return하는 함수를 전달할 수 있다고 한다. **즉, 새로 만드는 loss function은 `y_true`와 `y_pred`를 인수로 받아서 scalar 값을 return하는 함수여야 한다.**

## Custom Loss Function

이제 본격적으로 loss function을 만들어보자.

```python
import tensorflow.keras.backend as K

def custom_loss(y_true, y_pred):
  return K.mean(K.sum(K.square(y_true-y_pred)))

model.compile(optimizer='rmsprops',
             	loss=custom_loss,
             	metrics=['accuracy'])
```

위의 `custom_loss`는 사실 `loss='mean_squared_error'`로 간편하게 keras에서 제공하고 있지만 customizing하는 방법을 보여주기 위해 작성해보았다.



