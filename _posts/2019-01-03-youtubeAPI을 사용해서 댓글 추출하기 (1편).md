---
layout: post
title:  "youtubeAPI을 사용해서 댓글 추출하기 (1편)"
date:   2019-01-03 10:33:00 +0900
categories: [analysis]
use_math: true
---

## youtubeAPI을 사용해서 댓글 추출하기 (1편)

`Google` 에서는 많은 API을 제공하기 때문에 이를 활용한 어플리케이션을 만들 수 있다. 특히나 `youtube` API을 활용하여 꽤나 유용한 데이터 분석 결과를 보여주는 사이트들도 많은 걸 볼 수 있다. 이번 포스팅에서는 youtube에서 댓글 내용과 이에 관련된 메타데이터를 뽑아내는 프로젝트를 진행한 적이 있어 그 과정을 다뤄보려고 한다.

### Google API Service

youtube API을 사용하기 위해서 API KEY을 발급받아야 한다. 외부에서 무분별하게 API을 활용하여 서버의 과부화를 일으키거나 문제를 일으키는 것을 방지하기 위해 사용자를 인증하고 token을 발급받아야 하는 과정이 필요하다. ("저는 인증된 사용자이니 API을 호출하여 사용하겠습니다."의 과정이라고 보면 된다!) Google API을 사용해야 하니 당연하게도 Google 계정이 필요하다. 로그인 이후에 다음과 같은 URL로 접속해보자.

https://console.cloud.google.com/

상단 메뉴에 있는 프로젝트 목록을 열어보면 다음과 같은 화면이 나온다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

새로운 프로젝트를 생성해보자. 원하는 프로젝트 이름과 함께 프로젝트를 만들면 다음과 같은 화면을 만날 수 있다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/2.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

이제 API 사용설정을 해야하는데, 위 화면에서 `API 개요로 이동` 을 눌러보자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/3.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

위쪽에 `ENABLE APIS AND SERVICES` 을 클릭한다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/4.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기서 `youtube` 을 검색하면 `Youtube Data API v3` 을 누르고 `사용` 을 누르면 된다. 

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/5.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

이제 `youtube API` 을 사용할 준비를 마쳤다! 이제 본격적으로 API KEY를 발급해보자.



### OAuth 2.0 인증

`API KEY`, 그리고 `OAuth 2.0` 을 발급해야한다. 먼저 `OAuth 2.0`은 외부에서 서비스에 접근할 때, 신원을 증명하고 인증해야 하는데 이를 가능하게 하는 프로토콜이다. 우리가 평소에 유튜브에 댓글을 적을 때, 브라우저나 어플리케이션으로 들어가 로그인을 하고 댓글을 적는다. 하지만 우리는 API을 이용하여 우리가 만든 외부 서비스에서 이를 가능하게 해야하므로 이 `OAuth 2.0` 이 필요한 것이다. 

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/6.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

위 화면처럼 왼쪽 메뉴바의` 사용자 인증 정보`를 누르자. API 키는 나중에 발급하고, 먼저 OAuth 2.0 클라이언트를 주목해보자. 새로 만들기 위해 위에 있는 `사용자 인증 정보 만들기` 을 누르고 `OAuth 클라이언트 ID` 을 누르자. (제품 이름을 설정해야 한다는 경고가 뜨는 경우에 Oauth 동의를 설정하자. User Type을 `외부` 로 하고 어플리케이션 이름만 원하는 걸로 설정하고 저장하면 된다.)

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20190103/7.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

어플리케이션 유형은 `웹 애플리케이션` 으로 설정하고 위에 있는 내용으로 ID를 만들면 된다.



[2편으로 이어집니다]