---
layout: post
title:  "jupyter notebook에서 tensorflow 사용하기"
date:   2020-11-29 02:11:00 +0900
categories: [machinelearning]
use_math: true
---

## jupyter notebook에서 tensorflow 사용하기

머신러닝을 공부할 때, jupyter notebook을 사용하는 것이 편하다. 왜냐하면 방대한 데이터를 그때 그때 확인해가면서 코드를 작성할 수 있기 때문이다. 또한 이미 학습된 모델을 재사용할 때도 객체가 블럭화 되어 있어 사용하기 편하다. 이 포스팅은 **jupyter notebook을 설치하고 local에 tensorflow가 설치되어 있다는 가정하에 진행해 볼 것이다.**

Tensorflow는 보통 `docker` 나 `virtualenv` 를 통해 많이 사용할 것이다. 이는 가상 환경을 설정해주는 작업인데, 이렇게 해야 패키지 의존도가 높은 python 코드에서 충돌이 덜 일어나고 다양한 작업환경을 동시에 경험하는 경우 이를 분리시킬 수 있다.

<figure>
  <img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20201129/1.jpeg" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>
  <em>아년ㅇ</em>
</figure>

  

