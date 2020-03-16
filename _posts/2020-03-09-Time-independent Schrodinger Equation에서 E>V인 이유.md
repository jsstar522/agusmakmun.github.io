---
layout: post
title:  "[양자역학]Time-independent Schrodinger Equation에서 E>V인 이유"
date:   2020-03-09 05:07:00 +0900
categories: [physics]
use_math: true
---

> Show that E must exceed the minimum value of $V(x)$ for every normalizable solution to the time-independent *Schrodinger* equation. What is the classical analog to this statement?

`Time-independent 슈뢰딩거 방정식`에서 $E$가 $V(x)$의 최소값보다 항상 커야하는 이유를 설명하는 문제다. 먼저 original time-dependent 슈뢰딩거 방정식은 다음과 같다.

$\frac{-\hbar^2}{2m} \frac{d^2\psi(x,t)}{dx^2} + V(x)\psi(x,t) = i\hbar \frac{d\psi(x,t)}{dt}$

여기서 $\psi(x,t)$를 $u(x)T(t)$로 나누어서 가정할 수 있다.

$\psi(x,t) = u(x)T(t)$

이 식을 time-dependent equation에 대입하면,

$[-\frac{\hbar^2}{2m} \frac{d^2u(x)}{dx^2} + V(x)u(x)]T(t) = i\hbar u(x) \frac{dT(t)}{dt}$

로 쓸 수 있다. t에 관한 항을 오른쪽으로 모두 넘겨보자.

$\frac{[-\frac{\hbar^2}{2m} \frac{d^2u(x)}{dx^2} + V(x)u(x)]}{u(x)} = \frac{i\hbar\frac{dT(t)}{dt}}{T(t)}$

양쪽 항이 모두 같으려면 반드시 두항 모두 상수가 되어야 한다.(우변이 시간에 따라 변하지 않으므로) 이 상수를 $E$라고 하자. 그렇다면 다음과 같은 식이 도출된다. (나중에 계산해보면 이 $E$ 상수는 실제 에너지를 뜻한다)

$\frac{[-\frac{\hbar^2}{2m} \frac{d^2u(x)}{dx^2} + V(x)u(x)]}{u(x)} = \frac{i\hbar\frac{dT(t)}{dt}}{T(t)} = E$

$\frac{i\hbar\frac{dT(t)}{dt}}{T(t)} = E$

$i\hbar\frac{dT(t)}{dt} = ET(t)$ 이면,

첫번째로,

$T(t) = Ce^{-iEt/\hbar}$

두번째로,

$\frac{d^2\psi}{dx^2} = \frac{2m}{\hbar^2}[V(x)-E]\psi$

가 나온다.



이제 우리가 문제의 증명을 통해 사용할 식은,

$\frac{d^2\psi}{dx^2} = \frac{2m}{\hbar^2}[V(x)-E]\psi$

이다.

### 설명으로 증명

$V(x) > E$라고 가정하면 원함수와 이계도함수와 부호가 같다는 의미이므로, 도함수가 `항상 증가`하거나 함수가 `항상 감소`하는 형태일 것이다. 즉, 아래로 볼록하거나 위로 볼록한 그래프로 그려질 것이다. 

예를들어 원함수가 항상 양수라고 할 때, 이계도함수도 항상 양수이므로 원함수는 **항상 양수이면서 아래로 볼록한 함수일 것이다.** 반대로 둘다 음수일 경우에는 **항상 음수이면서 위로 볼록한 함수일 것이다.** 

이 전체 구간을 normalization하면 $\infty$ 혹은 $-\infty$으로 발산하므로 파동함수는 존재하지 않는다.

### 계산으로 증명

$V(x) > E$라고 가정하고 이 식을 미분방정식으로 풀기위해 다음과 같이 바꾼다.

$\frac{d^2\psi}{dx^2} = \kappa^2 \psi$

($V(x) < E$ 일 때, $\kappa^2$은 음수가 될 수 없다.)

미분방정식을 풀면,

$\psi(x) = Ae^{\kappa x} + Be^{-\kappa x}$

해가 존재하는지 알아보기 위해선 `Normalization of wave function`을 해보면 된다.

> 이는 막스 보른(Max Born)의 통계학적 해석을 기반으로 한 방식으로 파동함수를 이해하는 방법 중 하나라고 생각하면 된다. 파동함수를 제곱해서 적분하면 해당 구간(적분 구간)에 입자가 발견될 확률과 같다.



$\int_{-\infty}^{\infty}|\psi(x)|^2dx = \int_{-\infty}^{\infty} |Ae^{\kappa x}+Be^{-\kappa x}|^2dx$ 

$= [\frac{A^2}{2\kappa}e^{2\kappa x} + 2ABx + \frac{B^2}{-2\kappa}e^{-2\kappa x}]_{-\infty}^{\infty} = \infty$



$V(x) > E$인 구간에서는 확률이 무한대 이므로 해가 없음을 알 수 있다.

즉, 파동함수가 존재하지 않는다는 의미이다.