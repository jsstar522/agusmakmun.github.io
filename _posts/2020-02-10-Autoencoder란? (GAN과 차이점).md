---
layout: post
title:  "Autoencoder란? (GAN과 차이점)"
date:   2020-02-05 07:30:00 +0900
categories: [deeplearning]
use_math: true
---

`Deep Generative Model`에는 GAN 이전에 오랫동안 연구되어 왔던 `VAE(Variational Auto-Encoder)`라는 모델이 있다. `Ian Goodfellow`이 정리한 생성모델에 대한 Taxonomy(분류)가 있는데 살펴보자.

## Generative Models

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200210/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

방식에 따라 Generative Model을 분류했다. 먼저 Maximum Likelihood(최대 가능도 분포를 추정)을 토대로 `Explicit`와 `Implicit`으로 나뉜다. 

> 가능도는 확률분포와는 조금은 다른 개념이다. 연속성이 없는 사건에서는 같은 개념을 가지고 있지만 연속성이 있는 사건에서는 완전히 다른 개념을 가지고 있다. 주사위를 2번 던져서 6이 두번나오는 확률과 가능도는 같다. 하지만 표준정규분포에서 숫자 2개를 뽑았을 때 1이 2번 나올 확률은 0 (연속사건에서 범위가 아닌 특정사건이 일어날 확률은 무조건 0), 가능도는 0.24*0.24 (연속사건에서 특정사건이 일어날 가능도는 PDF의 y값)이다.

`Explicit`은 명백한 것을 의미하고, `Implicit`은 함축적인 것을 의미하는데, 여기에선 모델 확률분포($P_{model}$) 자체를 구하는 가, 모델 확률분포($P_{model}$)를 확실하게 정의하지 않고 sample을 뽑아서 확률분포를 간접적으로 알게되느냐에 따라 나뉜다. 

다시말해, `Explicit`는 모델 확률분포($P_{model}$)을 명확히 정의하고 이 확률 분포가 잘 맞는 쪽으로 계속 학습을 시킨다 (**latent vector 확률분포만 잘 학습시키면 sampling은 자동으로 좋아진다**). 하지만 `Implicit`는 명확하게 확률분포를 학습시키는데 목적을 두고 있지 않다. sampling이 좋냐 좋지 않냐를 두고 sampling을 하는 generator를 학습시킬 뿐이다.

그리고 Explicit 중에서도 확률분포 계산이 가능한 모델(`Tractable`)과 불가능한 모델(`Approximate`)로 나뉘는데, VAE는 계산 불가능한 모델을 다룬다. 

## Variational Inference

