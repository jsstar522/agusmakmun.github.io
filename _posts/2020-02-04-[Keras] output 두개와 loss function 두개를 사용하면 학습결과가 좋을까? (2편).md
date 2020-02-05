---
layout: post
title:  "[Keras] output 두개와 loss function 두개를 사용하면 학습결과가 좋을까? (2편)"
date:   2020-02-05 07:30:00 +0900
categories: [gan]

---

지난번엔 두개의 output을 뽑아내고 두개의 loss function을 적용시키는 것까지 해봤는데, **loss function을 같은 loss function으로 적용시켰다.** 이번엔 `각각 다른 loss function`을 적용시키면 좋은 결과를 만들어 낼지 포스팅할 예정이다.

## GAN의 Dicriminator는 어느정도 비슷해야 real or fake를 헷갈려 할까?

![distribution](https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200205/1.png)

가운데에 높은 값이 찍히는 `shape`도 중요하지만 `픽셀의 총합`도 중요한 상황이다. 이미지만 보았을 때 shape은 어느정도 비슷하다고 생각할 수 있지만 `픽셀의 총합`을 뽑아보면 만족스럽지 못한게 사실이다. **GAN의 Discriminator는 어느정도 비슷해야 real과 fake를 헷갈려하길래 이정도로 픽셀의 총합이 다를까?** 단순히 shape만 보고 real과 fake를 헷갈려 할까?



답은 discriminator의 능력에 달려있다. discriminator가 구별하는 능력이 좋다면 `shape` 뿐만 아니라 `픽셀의 총합`도 고려하여 real과 fake를 구별해낼 것이다. **픽셀의 총합의 loss를 줄이는 방향을 제시하기 위해 loss function을 정해주는 것이 학습방향에 도움이 될 것이다.** 그래서 기존에 사용하던 wasserstein loss 뿐만 아닌 또다른 loss function을 적용할 예정이다.

> Discriminator의 능력이 좋으면 Generator가 많은 것을 고려한(shape 뿐만 아닌 픽셀의 총합) 굉장히 유사한 이미지가 생성될 것이라는 생각과 다르게 Discriminator가 Generator에 비해 압도적으로 학습능력이 좋으면 Generator가 학습을 포기해버리는 현상이 나타난다. GAN에서는 `적당히`가 가장 중요하다.

어떤 loss function을 적용하는 것이 좋을까? 일단 가장 간단한 `mse(mean squared error)`를 적용해보기로 했다. 



## 2번째 Loss function

`mse`는 `cross entropy`와 함께 가장 많이 쓰이는 loss function이다.

$error = \frac{1}{n}\sum\limits_{i=1}^{n}(\hat{y}_i-y_i)^2$

큰 틀에서 mse도 예측값($$\hat{y}$$)와 실제값($$y$$)의 차이로 정의된다. 24x24 이미지의 경우로 생각해보자. 예측값도 24x24의 이미지이고 실제값도 24x24의 이미지일 때, 각 `픽셀들(576개의 값)`의 차이를 평균낸 것이다. 어느정도 픽셀의 총합에 대한 결과물이 좋아질 것이라는 예상을 할 수 있다.

먼저 mse loss function을 만들자. (그냥 mse를 사용해도 되지만, 나중에 커스터마이징을 해볼 생각이라 함수를 직접 만들어봤다.)

```python
def mse(y_true, y_pred):
  return K.mean(K.square(y_pred-y_true), axis=-1)
```

트레이닝을 하면서 (루프를 돌면서) `train_on_batch`이나 `fit`을 통해 학습을 할텐데, loss function을 다음과 같이 적용하려면 `예측값(output)이 24x24이미지`이어야 하고 `label도 24x24이미지`이어야 한다.

```python
#...
def create_generator():
  G = Sequential()
  G.add(Dense(6*6*128, input_dim=latent_dim))
  G.add(Reshape(6, 6, 128))
  G.add(BatchNormalization(momentum=0.9))
 	G.add(LeakyReLU())
  G.add(Dropout(0.3))
  
  G.add(UpSampling2D())
  G.add(Conv2DTranspose(64, 3, padding='same'))
  #...
  return G

def create_discriminator():
  D = Sequential()
  D.add(Conv2D(16, 3, strides=2, padding='same', input_shape=(24, 24, 1)))
	D.add(LeakyReLU())
	D.add(Dropout(0.3))
  #...
  return D
#...

## Discriminator Network
discriminatorNets = create_discriminator()
discriminatorModel = discriminatorNets
optim_discriminator = RMSprop(lr=0.0008, clipvalue=1.0, decay=1e-10)
discriminatorModel.compile(loss=wasserstein_loss, optimizer=optim_discriminator, metrics=['accuracy'])

## Adversarial Network
for layer in discriminatorModel.layers:
  layer.trainable =False
generatorNets = create_generator()

gan_input = Input(shape=(latent_dim, ))
x = generatorNets(gan_input)
gan_output = discriminatorNets(x)
optim_adversarial = RMSprop(lr=0.0004, clipvalue=1.0, decay=1e-10)
## 2 output
ganModel = Model(gan_input, [gan_output, gan_output])
## 2 loss function
ganModel.compile(loss=[wasserstein_loss, wasserstein_loss], optimizer=optim_adversarial, metrics=['accuracy'])

#...
real_images = x_train[start: stop]
#...
g_loss = ganModel.train_on_batch(noise, [labels_real, labels_real])
#...
```

위의 코드는 지난 포스팅에서 같은 wasserstein loss를 적용했었던 코드다. 여기서 loss function 부분과 label 부분만 바꾸어주면 될까? 그렇지 않다. `gan_output`이 `(1, )`차원이기 때문에 에러가 발생한다. 

![distribution](https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200205/2.jpeg)

위 그림처럼 하나의 network에 두개의 loss function이 적용된다. 각각 다른 `loss function` 뿐만 아니라 `output shape`, `label shape`도 다르다.

- 첫번째 갈래는 `generatorNets(gan_input)`을 통해 나온 24x24이미지를 통해 loss를 계산(`mse`)하고 학습시킴
- 두번째 갈래는 `discriminatorNets(generatorNets(gan_input))`을 통해 나온 (1, )차원의 값을 통해 loss를 계산(`wasserstein loss` or `cross entropy`)하고 학습시킴

다음과 같이 바뀌여야 한다.

```python
#...
def create_generator():
  G = Sequential()
  G.add(Dense(6*6*128, input_dim=latent_dim))
  G.add(Reshape(6, 6, 128))
  G.add(BatchNormalization(momentum=0.9))
 	G.add(LeakyReLU())
  G.add(Dropout(0.3))
  
  G.add(UpSampling2D())
  G.add(Conv2DTranspose(64, 3, padding='same'))
  #...
  return G

def create_discriminator():
  D = Sequential()
  D.add(Conv2D(16, 3, strides=2, padding='same', input_shape=(24, 24, 1)))
	D.add(LeakyReLU())
	D.add(Dropout(0.3))
  #...
  return D
#...

## Discriminator Network
discriminatorNets = create_discriminator()
discriminatorModel = discriminatorNets
optim_discriminator = RMSprop(lr=0.0008, clipvalue=1.0, decay=1e-10)
discriminatorModel.compile(loss=wasserstein_loss, optimizer=optim_discriminator, metrics=['accuracy'])

## Adversarial Network
for layer in discriminatorModel.layers:
  layer.trainable =False
generatorNets = create_generator()

gan_input = Input(shape=(latent_dim, ))
x = generatorNets(gan_input)
gan_output = discriminatorNets(x)
optim_adversarial = RMSprop(lr=0.0004, clipvalue=1.0, decay=1e-10)
######################################바뀌는 부분(1)########################################
#ganModel = Model(gan_input, [gan_output, gan_output])
ganModel = Model(gan_input, [gan_output, x])
######################################바뀌는 부분(2)########################################
#ganModel.compile(loss=[wasserstein_loss, wasserstein_loss], optimizer=optim_adversarial, metrics=['accuracy'])
ganModel.compile(loss=[wasserstein_loss, mse], optimizer=optim_adversarial, metrics=['accuracy'])

#...
real_images = x_train[start: stop]
#...
######################################바뀌는 부분(3)########################################
#g_loss = ganModel.train_on_batch(noise, [labels_real, labels_real])
g_loss = ganModel.train_on_batch(noise, [labels_real, real_images])
#...
```

특히나 `바뀌는 부분(1)`을 주목해볼 필요가 있는데, 엄밀히 얘기하면 **저 부분에서 layer가 두갈래로 나뉘어진다고 볼 수 있다.** 

결과는 어땠을까? (여전히 같은 epochs와 같은 hyper parameter를 사용했다.)

![distribution](https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200205/3.png)

**놀라울정도로 좋아졌다.** spread한 range를 가지고 있던 데이터들이 특정한 곳으로 많이 몰려있음을 확인할 수 있다. Discriminator를 너무 자율적으로 학습시키는 것보다 일종의 `가이드라인`을 제시해준 것이 큰 도움이 되었다. 단순히 `mse`만 추가 했는데도 결과가 좋아졌다. 사실 `mse`는 거시적인 기준이라 세부적으로 들어가면 error가 큰 부분이 있을 수 있다. 

역시나 각 픽셀값들을 뽑아서 distribution을 그려보았는데, 중앙에 있는 값들에 대한 차이가 생각보다 많이 났다. 이 부분을 개선할 예정이다.