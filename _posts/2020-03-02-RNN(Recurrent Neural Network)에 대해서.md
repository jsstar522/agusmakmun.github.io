---
layout: post
title:  "RNN(Recurrent Neural Network)에 대해서"
date:   2020-03-02 05:07:00 +0900
categories: [deeplearning]
use_math: true
---

RNN은 `문단`, `자연어 처리` , `이미지 caption`, `번역`  등등 `sequence data (or 시계열 data)`에서 사용되는 모델이다. 본론으로 들어가기 전에 어떤 식으로 사용되는지 알아보고 설명을 이어나가는게 이해하는 데에 더 효과적일 것 같다.

## 어떻게 사용 되는가?

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200302/1.jpg" alt="distribution" style="width:700px; margin: 0 auto;"/>

`one to one`은 RNN이라기 보다 그냥 일반적인 **NN 모델**(neural network)이다. `one to many`의 예시에는 **Image Caption**이 있다. 하나의 이미지를 보고 여러 단어를 통해 문장을 만들어내는 과정이다. `many to one`의 예시에는 문장의 성격을 알아내는 **Sentiment Classification**이 있다. 여러 단어의 문장을 보고 긍정, 부정 혹은 감정등을 유츄해낼 수 있다. `many to many`의 대표적인 예시에는 **번역 및 연관어 검색**이 있다.



## 어떻게 작동 되는가?

RNN의 핵심은 이전 입력값들을 **기억**하고 있어서 새로운 입력값이 영향을 받는 구조다. 우리는 문장을 이해할 때, 매번 단어 하나하나를 분류하고 해석한 뒤 문장을 이해하지 않는다. 먼저 읽었던 부분을 기억하고 있다면 다음에 나오는 부분을 읽을 때 영향을 끼친다. 이러한 **기억**은 RNN에서 `hidden state`로 불린다.



> 우리는 "나는 불행하게도 오늘 지갑을 잃어버렸기 때문에 정말 기분이 좋지 않다."라는 문장을 읽을 때, "불행하게도"라는 부분만 읽고 나서도 벌써 부정적인 문장임을 무의식 중에 느끼고 읽는다.



<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200302/2.png" alt="distribution" style="width:700px; margin: 0 auto;"/>

$h_t$는 시간이 t일 때, state를 의미한다. $W_{hh}$, $W_{xh}$, $W_{hy}$는 model의 파라미터이고, 모든 state를 반복하는 동안 모두 같은 파라미터 값을 사용한다. state($h_{t}$)는 다음과 같이 정의된다. (~~위 그림에서 $o_t$를 $y_t$로 생각하자~~)

$h_{t} = tanh(W_{hh}h_{t-1} + W_{xh}x_{t} + b_{h})$

여기서 $W_{hh}$는 위 그림에서 $V$, $W_{xh}$는 위 그림에서 $U$라고 할 수 있다. 활성함수로는 **tanh**가 사용되었다. 



> 값이 층층이 쌓이는 네트워크 구조에서는 활성함수가 선형이 아닌 비선형 함수가 되어야 한다. 선형함수가 세번 겹쳐져 있으면 그대로 선형함수이기 때문에 효과를 볼 수 없다. ( $f(x) = a*x$ 일 때, $f(f(f(x))) = a^3x$ )



그리고 $y_{t}$는 다음과 같이 정의된다.

$y_{t} = W_{hy}h_{t} + b_{y}$

$h_{t}$를 보면 값에 영향을 미치는 두가지의 값이 `선형결합`을 이루고 있음을 알 수 있다. 하나는 $x_{t} (input)$이고, 하나는 $h_{t-1} (state)$이다. 즉, **이전의 값이 새로운 값을 만드는데 영향을 주는 것을 알 수 있다.** 

잘 보면 우리가 일반적으로 사용하는 Neural Network와 RNN은 크게 다르지 않다는 것을 알 수 있다. 위 그림을 보면 하나의 network가 복사된 형태를 가지고 있다.



## 문제점

RNN은 `장기 의존성(Long-Term Dependency)`라는 문제점을 가지고 있다. 이 장기 의존성 문제를 간단한 예시로 알아보자. 먼저 **두가지 정보의 위치가 가까운 경우**를 살펴보자. "The **boat** is on the **water**." 라는 문장을 학습시킨 후, "The **boat** is on the -- ."라는 문장을 주면 network는 쉽게 빈칸에 **water**라는 단어를 넣을 수 있다. 이는 **두 단어 정보의 위치가 가깝기 때문**이다. 하지만 "The **boat** is on the water. A **sailor** is driving that."라는 두 문장이 있다고 해보자. 우리는 **boat**와 **sailor**가 연관단어인 것을 알고 있지만, 두 단어의 `gap`이 크기 때문에 쉽게 예측하지 못한다.



## LSTM으로 해결!


