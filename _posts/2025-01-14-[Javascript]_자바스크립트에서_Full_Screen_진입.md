---
layout: post
current: post
cover: "assets/images/cover/javascript.png"
navigation: True
title: "[Javascript] 자바스크립트에서 Full Screen 진입"
date: 2025-01-14 02:14:00
tags:
    - [JavaScript, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

# [Javascript] 자바스크립트에서 Full Screen 진입


게임에서 처음 시작하면 자동으로 전체화면이 되는걸 구현해보려고



```
javascript
document.documentElement.requestFullscreen()


```



전체화면을,



```
javascript
$(document).ready()


```



웹페이지가 로딩이 되고 난 후에 자동으로 전체화면이 되도록 코드를 짜봤다.


결과는 실패였다.


검색을 해보니 보안 상의 이유로 유저의 트리거가 없는 한, 자동으로 전체화면은 불가능하다고 한다.
유저의 트리거를 강제로 써야했기 때문에, 유저가 게임 시작 버튼(트리거)를 누르는 순간 전체화면으로 바꾸는 방향으로 노선을 틀었으나 또 하나의 문제가 있었다.


화면의 크기가 달라지는 순간, 맵의 크기도 달라져야 하기 때문에 전체화면이 된 후에, 맵의 사이즈를 결정할 수 있다.



```
javascript
document.documentElement.requestFullscreen()


```



requestFullscreen 는



```
javascript
Promise


```



Promise, 즉 비동기로 실행이 되는데 then 과 catch 를 사용해서 resolve 를 실행해보아도, 이 requestFullScreen 의 로직이 독특한게


전체화면시키고자 하는 Element을 전체화면으로 만들 수 있는지 여부를 체크해서 resolve 와 reject 로 return 되고,


그 뒤에 element를 전체화면으로 만드는 것 같았다.


다시 말해서, 해당 element가 전체화면으로 만들 수 있는 것 까지만 Promise 이고, elment가 전체화면이 되기 전에 then 으로 넘어가버린다..


나는 window 의 resize 이벤트를 통해서 위 문제를 해결할 수 있었는데, resize가 되는 순간에 맵의 크기도 resize 시키는 방식으로 해결했다.

