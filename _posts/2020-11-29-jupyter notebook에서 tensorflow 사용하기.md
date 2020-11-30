---
layout: post
title:  "Jupyter notebook에서 tensorflow 사용하기"
date:   2020-11-29 02:11:00 +0900
categories: [machinelearning]
use_math: true
---

## jupyter notebook에서 tensorflow 사용하기

머신러닝을 공부할 때, jupyter notebook을 사용하는 것이 편하다. 왜냐하면 방대한 데이터를 그때 그때 확인해가면서 코드를 작성할 수 있기 때문이다. 또한 이미 학습된 모델을 재사용할 때도 객체가 블럭화 되어 있어 사용하기 편하다. 이 포스팅은 **jupyter notebook을 설치하고 local에 tensorflow가 설치되어 있다는 가정하에 진행해 볼 것이다.**

Tensorflow는 보통 `docker` 나 `virtualenv` 를 통해 많이 사용할 것이다. 이는 가상 환경을 설정해주는 작업인데, 이렇게 해야 패키지 의존도가 높은 python 코드에서 충돌이 덜 일어나고 다양한 작업환경을 동시에 경험하는 경우 이를 분리시킬 수 있다.

<figure>
  <img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20201129/1.jpeg" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>
  <center><em>책상은 더러워도 작업환경은 분리해서 깔끔하게!</em></center>
</figure>

어쨋든, jupyter notebook을 이 가상환경 위에 설치한 뒤 실행하면 자연스럽게 가상환경 위에 설치된 패키지를 그대로 사용할 수 있을 것이라고 생각했지만 그렇지 않다. **Jupyter notebook이 가르키는 경로를 가상환경 경로로 바꾸어 줘야하는데, 기존에 있던 경로를 바꾸지 않고 새로운 커널을 만들 것이다.**



 Kenel을 만들기 위해서 가상환경 위에 `ipykenel` 이라는 모듈을 설치해야 한다. 가상환경을 실행 한 뒤 `pip install ipykernel` 을 입력하면 된다.

```bash
$ source tfenv/bin/activate
$ (tfenv) pip install ipykernel
```

나의 가상환경은 `tfenv` 라는 이름을 가지고 있는 가상환경이기 때문에 위와 같지만 각자 올바른 경로로 가상환경을 실행하면 된다.

Jupyter 경로를 찾아 kernel을 추가할 것이다. 보통 맥의 경우 다음과 같은 경로에 jupyter가 설치되어 있다.

```bash
/Users/username/Library/Jupyter
```

이 경로에 폴더와 파일을 만들어 주자.

```bash
$ mkdir /Users/username/Library/Jupyter/kernels/tensorflow
$ vi kernel.json
```

`kernel.json` 이라는 파일안에 다음과 같은 내용을 넣는다.

```json
{
	"argv": [ "/Users/username/Documents/Projects/ML/tfenv/bin/python3", "-m", "ipykernel", "-f", "{connection_file}"],
	"display_name": "tensorflow",
	"language": "python"
}
```

맨 앞에 있는 경로는 가상환경의 python이 설치된 경로를 넣어줘야 한다. 가상환경을 실행한 뒤 `which python` 을 입력하면 경로를 알 수 있다.

<figure>
  <img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20201129/2.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>
  <center><em></em></center>
</figure>

이제 모든 창을 닫고 다시 jupyter notebook을 실행시켜 새로운 `notebook 만들기`를 클릭하면 내가 만든 가상환경의 notebook을 생성시킬 수 있다.