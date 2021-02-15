---
layout: post
title:  "[intellJ]No main manifest attribute 해결"
date:   2020-12-07 17:40:00 +0900
categories: [java]
use_math: true
---



jar 파일을 만들어서 실행할때 Manifest가 없어서 실행이 안된다는 오류

Manifest가 없거나 경로가 잘못 잡혀있어서

다시 생성해봐



Project structure - artifacts 메뉴 - +버튼으로 artifact 등록

jar - from modules with dependencies

module 선택

메인클래스 선택

MANIFEST.MF가 생성되는 디렉토리를 resource로 선택

(java로 선택되는 것은 버그)