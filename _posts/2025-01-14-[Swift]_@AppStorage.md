---
layout: post
current: post
cover: "assets/images/cover/swift.png"
navigation: True
title: "[Swift] @AppStorage"
date: 2025-01-14 03:52:00
tags:
    - [Swift, ]
class: post-template
subclass: 'post'
author: 

categories:
    - [Programming, ]
---

# @AppStorage


Swift 에서도 코틀린처럼 기기 내부에 데이터를 저장할 수 있는 방법이 있다.


코틀린에서는 SharedPreference 를 사용했었는데, Swift 에서는 @AppStorage 를 사용한다.


다만 차이점이 있었는데,



```
text
@AppStorage("onboarding") var isOnboardingViewActive : Bool = true


```



위의 코드대로만 보면 어느 파일에서든 "onboarding" 프로퍼티를 사용하려면 true(혹은 false) 로 초기화를 해야하는 것처럼 보인다.


> 사실은 계속 초기화가 되는 것이 아니라, 기기 내부 저장소에서 onboarding 프로퍼티를 찾지 못하는 경우에만 true로 초기화 한다는 뜻이고, 이미 존재한다면 해당 초기화 구문은 무시된다고 한다.

