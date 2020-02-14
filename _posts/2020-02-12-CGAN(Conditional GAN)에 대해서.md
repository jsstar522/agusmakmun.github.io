---
layout: post
title:  "CGAN(Conditional GAN)에 대해서"
date:   2020-02-12 11:01:00 +0900
categories: [gan]
use_math: true
---

`CGAN`은 나온지 시간이 꽤 지난 GAN의 응용모델이지만 GAN을 이용한 연구에 많은 영감을 주고 있다. Vanilla GAN은 real 이미지를 주면 real 이미지와 비슷한 fake 이미지를 만들어 내지만, fake 이미지를 만들어 내는 과정을 관여할 수는 없다. **MNIST 데이터를 예를 들어보자면 Vanilla GAN의 generator는 숫자를 무작위로 만들어낼 뿐이다. 하지만 CGAN은 숫자 1을 원하면 숫자 1을 뽑아낼 수 있다.**

## 어떻게 작동되는가?

### latent vector

generator에 들어가서 이미지를 만들어내기 전의 임의의 noise를 `latent vector`라고 칭한다. `VAE`와는 다르게 `GAN`은 latent vector가 존재하는 `latent space`의 확률분포를 정의하지 않는다. 즉, latent vector를 해석할 수 없다는 뜻이다. 해석할 수 없다는 뜻은 **latent vector 중에 어떤 요소가 어떤 특징을 담당하고 있는지 알 수 없다는 뜻이다.** 그래서 어떤 특징에 대한 것들을 labeling하고 함께 학습을 시키면 label에 따라서 fake 이미지가 정리될 수 있다. 

MNIST 데이터를 통해 쉽게 설명하자면, 0부터 9까지 가지고 있는 가장 쉬운 특징은 숫자 모양 그 자체일 것이다. 그 종류는 10종류이다. 이 10개의 label과 함께 학습을 시키는 것이다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200212/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

### loss function

각 network(`discriminator nets` & `adversial nets`)의 output은 어차피 0~1 범위로 동일하므로 기본적인 `binary_crossentropy`를 사용하면 된다.



## 구현

MNIST 데이터를 통해 0~9까지 원하는 숫자를 뽑아내보자.

주의할 점은 앞서 얘기한 바와 같이 input이 2개(`label이 추가된`)로 들어간다는 것이다. discriminaotor를 예로 보면 다음과 같은 형태로 들어간다.

```python
def create_discriminator(self):
  D = Sequential()
  D.add(Dense(512, input_dim=self.width*self.height*self.channel))
  D.add(LeakyReLU(alpha=0.2))
  D.add(Dense(512))
  D.add(LeakyReLU(alpha=0.2))
  D.add(Dropout(0.4))
  D.add(Dense(512))
  D.add(LeakyReLU(alpha=0.2))
  D.add(Dropout(0.4))
  D.add(Dense(1, activation='sigmoid'))

  D.summary()

  img = Input(shape=(self.width, self.height, self.channel))
  flat_img = Flatten()(img)
  ## label for cgan
  label = Input(shape=(1, ), dtype='int32')
  label_embedding = Flatten()(Embedding(self.num_classes, self.width*self.height*self.channel)(label))

  model_input = multiply([flat_img, label_embedding])
  output = D(model_input)

  return Model([img, label], output)
```

`input`은 28x28 이미지를 넓게 편(Flatten) 값들과 각각의 label들이 합쳐진 형태로 들어가게 된다.

```python
  label_embedding = Flatten()(Embedding(self.num_classes, self.width*self.height*self.channel)(label))
```

 이 layer는 다음과 같은 모양으로 만들어진다.



