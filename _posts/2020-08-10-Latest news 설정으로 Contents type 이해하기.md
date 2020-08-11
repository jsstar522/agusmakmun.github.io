---
layout: post
title:  "Latest news 설정으로 Contents type 이해하기"
date:   2020-08-10 09:13:03 +0900
categories: [drupal]
---

## Latest news 설정으로 Contents type 이해하기

꽤나 괜찮은 drupal theme을 설치하면 메인 페이지에 `Latest News` 을 설정할 수 있게 끔 Blocks (Views)을 만들어 놨을 것이다. 내가 설치한 theme의 경우에는 `front page` 라는 view에 고정으로 latest news의 기능을 하는 것이 만들어져 있었다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

하지만 골치 아픈일인 것이, 모든 contents을 불러와서 latest news로 가져오다보니 slideshow에서 사용되는 banner contents도 표시된다는 점이다. (위 사진에 보면 banner1, banner2은 slideshow을 위한 이미지이다.) 이를 거르기 위해 contents type을 구분하고, views 설정에서 이를 filter에 추가해주면 된다. 먼저 이 latest news에서 article만 보여주기 위해서 article type에서 새로운 field을 만들어 보자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/2.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Structure의 Content types에서 Article의 Manage fields을 선택한 화면이다. 위 그림은 내가 이미 수정해 놓은 사항인데, 원래는 **첫번째 줄에 Body라는 이름의 field가 있었을 것이다.** 이 Body field는 다른 content type (예를들어 banner, teammate와 같은 다른 content)에도 들어가는 field이기 때문에 이와 구분짓는 다른 이름을 가진 새로운 field가 필요하다. **모든 setting은 똑같지만 field 이름만 다르면 된다!** 기존에 있던 Body을 지우고 `Add field` 을 클릭하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/3.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Body을 지웠다면 Re-use an existing field 탭에 위 그림과 같은 메뉴가 있을 것이다. 이를 선택하고 Label을 Body_article로 지정한 뒤 Save을 하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/4.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기에선 딱히 건드릴 것이 없다. Summary input만 체크하고 저장하도록 하자. 이제 이전과 같이 article content을 생성하면 글과 이미지을 작성할 수 있지만 글의 field가 body_article로 저장된다. 새로 만든 Body의 위치조정을 위하여 다시 Article의 contents type 설정 페이지로 가보자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/5.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

설정 메뉴에서 `Manage form display` 을 클릭한다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/6.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

새로운 field (`Body_article`)이 가장 밑으로 내려가 있을 것이다. 위치를 위의 그림과 같이 옮겨주고 `WIDGET` 을 위와 같이 수정한다. 다음은 `Manage display` 탭으로 가서 나머지를 수정하자.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/7.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

새로운 field가 `Disabled` 에 있을 것이다. 위 그림과 같이 위치와 `LABEL`, `FORMAT` 을 바꿔준다. 이어서 현재 설정화면에서 `Teaser` 탭으로 간다. 

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/8.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

정확하게는 모르겠지만 미리보기를 설정하는 곳인 것 같다. 이 설정에 따라 Latest News의 생김새가 달라질 것이다. 위 그림과 같이 바꾼다. Format은 summary로 해주어야 Latest News에서 글의 길이가 길때, 잘려서 보인다.

이제 모든 준비가 끝났다. Latest News에 해당하는 Views을 찾아가서 filter에 `Body_article` 을 설정해주면 된다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/9.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

Views 설정화면에서 `FILTER CRITERIA` 에 Body_article을 추가해주기 위해 `Add` 을 클릭한다. 

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/10.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

위 그림처럼 설정하고 저장을 눌러준다. 이제 기존에 있던 article은 모두 지우고 새롭게 article을 만든 뒤, 확인해보면 banner나 다른 page와 같은 content가 아닌 오직 article만 불러와서 Latest News에 띄우는 것을 확인할 수 있다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200810/11.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>



