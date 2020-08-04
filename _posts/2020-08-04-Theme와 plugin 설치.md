---
layout: post
title:  "Theme와 plugin 설치"
date:   2020-08-04 19:11:03 +0900
categories: [drupal]
---

## Theme 설치

Drupal에서 theme은 간단하게 설치할 수 있다. Drupal 홈페이지의 theme 탭으로 들어가면 다양한 theme을 제공하고 있다. https://www.drupal.org/project/project_theme 에 들어가서 원하는 theme을 선택하자. 가장 기본적인 `Adminimal` theme을 클릭해서 보면 다음과 같다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기서 아래로 내려보면 다운로드를 drupal 버전별로 제공하고 있다. 나는 drupal 8을 사용하고 있으므로 `8.x` 을 선택했고 `zip` 파일과 `tar.gz` 을 선택할 수 있다. **여기서 바로 다운로드를 하지말고 `tar.gz` 파일의 링크 경로를 복사하자.**

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/2.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

그리고 만들어 놓은 Drupal 사이트에 관리자 계정으로 로그인하면 관리자 탭에서 `Appearance` 를 선택한 뒤, `Install new theme` 을 누르면 다음과 같이 나온다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/3.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

여기에 URL을 붙여놓고 Install을 누르면 설치가 완료된다. 이제 다시 `Appearance` 에 들어가면 theme가 설치되어 있는 것을 볼 수 있고, 해당 theme에서 `Set as default` 버튼을 누르면 theme이 적용된다.



## Plugin 설치

Drupal의 장점은 수많은 plugin을 원하는대로 가져와서 사용할 수 있다는 점이다. plugin도 theme과 같은 방식으로 다운로드 할 수 있다. https://www.drupal.org/project/project_module 에 들어가서 원하는 plugin (module)을 선택하자. 그리고 theme과 같은 방식으로 `tar.gz` 파일의 링크를 복사하면 된다. 그리고 `Install new module` 을 눌러 URL을 붙여넣은 뒤 설치하면 끝난다. 이는 압축을 풀어놓은 상태이므로 완벽하게 module 설치를 끝마치려면 해당 module을 찾아서 활성화를 시켜야 한다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200804/4.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

`FlexSlider` 라는 module을 설치한 이후, 검색을 해보면 위 그림과 다르게 체크표시가 비활성화 되어 있을 것이다. 체크표시를 하고 밑에 `Install` 버튼을 눌러줘야 완벽하게 설치가 끝난다.

