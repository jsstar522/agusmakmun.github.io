---
layout: post
title:  "ACGAN(Auxiliary Classifier GAN)에 대해서"
date:   2020-02-19 05:07:00 +0900
categories: [gan]
use_math: true
---

`ACGAN`은 `CGAN`의 응용버전이다. 목적은 `CGAN`처럼, `latent vector`를 제어해서 원하는 fake image를 뽑아내기 위함이다. 학습하는 방식은 약간 다르다.

## 어떻게 작동되는가?

**CGAN과 다른점은 discriminator가 image class를 input으로 받지 않는다는 점이다.** 또한 discriminator가 real/fake를 output으로 내뱉었지만 ACGAN에서는 **class까지 판별**한다. 즉, output이 2개이다. 그래서 loss function이 2개다!

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200219/1.jpeg" alt="distribution" style="width:700px; margin: 0 auto;"/>

맨 오른쪽에 있는 모델이 ACGAN이다. 

