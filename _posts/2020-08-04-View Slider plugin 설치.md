---
layout: post
title:  "View Slider plugin 설치"
date:   2020-08-04 23:54:21 +0900
categories: [drupal]
---

## View Slider Plugin

보통 홈페이지를 보면 맨 앞에 슬라이드쇼가 있는 것을 알 수 있다. 나도 Slider가 꼭 들어갔으면 하는 마음에 기본적으로 Slider를 제공하는 theme을 설치했지만 제대로 작동하지 않았다. 그래서 가장 많이 사용되는 `View slider` plugin을 설치했다. https://www.drupal.org/project/views_slideshow 에서 설치할 수 있다.



## Slider 만들기

새로운 모듈을 깔았으니 이 모듈이 적용된 `block`을 만들어야 한다. 설치한 모듈을 적용할 block은 `views`에서 만들고 설정한다.

그 전에 slider에 들어갈 이미지를 모두 불러오기 위한 `content types`이 필요하다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/6.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기에서 `Add content type` 을 눌러 새로운 content type을 생성하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/7.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Banner라는 이름으로 content type을 만들었다. 이름은 상관없다. 그리고 `Display settings` 에서 작성자와 날짜를 표시하는 옵션만 꺼주면 딱히 건들 것은 없다. 이대로 생성해주면 된다. 생성하면 `Manage fields` 라는 탭이 생기는데, 여기서 content의 type을 설정할 수 있다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/9.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Default로 `Body` 가 들어가 있는데, 이건 지워주고 `Add field` 를 눌러 새로 생성하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/10.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Slider에는 이미지가 들어가야하므로 위와 같이 만들어주면 된다.

이제 `Add content` 에 들어가보면 Banner라는 타입하나가 생긴 것을 확인할 수 있다. 이 타입으로 이미지를 만들면 만드는대로 slider에 이미지가 추가 될 것이다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/8.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Banner로 이미지 2개를 만들어두자. 이제 Block을 만들어야 하는데, `views` 에 들어가서 만들면 된다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/5.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기에서 `Add view` 를 눌러 새로운 view을 생성하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/11.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

이제 Banner의 content type을 가진 모든 contents를 이 view (block)에서 보여줄 것이다. 어떻게 보여지는지는 앞서 설치한 모듈 `View slider` 모듈의 도움을 받아 설정할 것이다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/12.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

이 화면에서 view를 설정하면 되는데, `FORMAT` 의 `Slideshow Setting` 에 들어가면 우리가 설치한 `View slider` 모듈 규칙에 맞게 slide 속도, 시간 등을 설정할 수 있다. 그리고 아래에 있는 `Preview` 에는 앞서 만들어 둔 2개의 Banner content가 보이는 것을 알 수 있다.

이제 저장한 뒤, Block에서 Banner을 찾아 위치시키면 Slider가 잘 보이는 것을 알 수 있다.



## Error: $(...).cycle is not a function 오류

처음에는 slide가 넘어가지 않는 오류가 발생했는데, 개발자 콘솔에 들어가면 다음과 같이 에러메시지를 띄우고 있었다.

```javascript
Error: $(...).cycle is not a function
```

cycle 함수를 불러오지 못한다는 것은 라이브러리를 제대로 불러오지 못한다는 것인데 구글링을 통해 해결방법을 찾았다. (나중에 알게 되었지만 이러한 문제점이 발생하는 사람들이 많았던 탓인지 `View slider` 모듈 안에 있는 README 파일에도 해결방법을 적어놨었다.)

### 1. 모듈이 설치된 곳 확인

모듈이 설치된 곳에서 `libraries.yml` 파일을 열어보면 참조하는 자바스크립트 파일이 `libraries/jquery.hoverIntent/jquery.hoverIntent.js`, `libraries/jquery.cycle/jquery.cycle.all.js`, `libraries/json2/json2.js`, `libraries/jquery.pause/jquery.pause.js` 가 있다. 하지만 저 오류가 뜨는 경우에 이 파일들을 찾아보면 모두 없을 것이다.

> Views slider 모듈은 Views slider 모듈과 그 안에 Views slider cycle 모듈 두가지로 작동하는데 이해하기 헷갈리게 설치되어 있다. 그렇기 때문에 libraries.yml 파일도 두개이다.

### 2. 자바스크립트 파일 설치

이 파일을 `Views slider` 와 `Views slider cycle` 모듈 안에 있는 `libraries.yml` 파일을 참고하여 해당하는 경로에 맞게 파일을 설치해주어야 하는데, 각 파일의 설치 방법은 다음과 같다.

```bash
wget https://malsup.github.io/jquery.cycle.all.js
wget https://raw.githubusercontent.com/briancherne/jquery-hoverIntent/master/jquery.hoverIntent.js \
wget https://raw.githubusercontent.com/douglascrockford/JSON-js/master/json2.js \
wget https://raw.githubusercontent.com/tobia/Pause/master/jquery.pause.js
```

설치파일과 `yml` 파일에서 가르키는 설치된 경로가 잘 맞으면 이제 slider가 잘 작동된다.