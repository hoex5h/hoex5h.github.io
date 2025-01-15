---
layout: post
current: post
cover: "assets/images/cover/javascript.png"
navigation: True
title: "[Javascript] Deep Copy, Swallow Copy"
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

# [Javascript] Deep Copy, Swallow Copy


자바스크립트로 게임을 만들면서 캐릭터 객체가 들고 있는 방패 너머로 몬스터가 넘어오지 못하게 막는 방법을 구현하고 있었다.


이전에 방해물(Obstruct)클래스를 따로 만들어두었는데 방패도 몬스터의 방해물에 속하기 때문에, Obstruct 클래스에 방패도 프로퍼티로 넣어주었다.


이때 방패와 벽과 같은 방해물과의 다른 점이 있었는데, 방패는 캐릭터가 움직이면 같이 움직이는 방해물이고 벽은 움직이지 않는 방해물이었다.


(후에 움직이는 벽을 만들거지만, 일단은 지금은 맵을 감싸는 벽이기 때문)



```
javascript
  if( monsterObstacle.getStuck(monster.coordinates) )
    continue;
  monster.move();


```



코드를 간략하게 설명하면 이런 식으로, 몬스터의 좌표를 인자르 넘겨 확인해서 방해물에 걸리지 않는다면 움직이도록 구현되었는데



```
javascript
monsterObstacle = {
 this.arr = [Wall, Shield];
}


```



몬스터의 방해물에는 현재까지 움직이지 않는 벽과 움직이는 방패가 있다.


여기서 Wall과 Shield의 좌표값이 주어지는데, 방패의 좌표값은 캐릭터가 움직일 때 마다 바뀌어야하는 것이 문제였다.


처음에는 방패라는 객체를 만들지않고 방패의 좌표값을 업데이트 시키는 방식을 택했었는데, 터무니 없는 방법이란걸 깨닫는데 10분도 걸리지 않았다.


자바스크립트에서 객체를 변수에 할당하면, 해당 객체의 참조값이 할당되는


얕은복사(자바스크립트에서는 얕은복사와 깊은복사라는 말을 잘 쓰지 않는다고 들었지만)를 선택하는 것이 더 좋은 판단이라고 생각했고



```
javascript
class Shield{
  this.#coordinates = {
    x : 0,
    y : 0,
  }
}


```



이런식으로 방패 객체를 만들어서 Monster Obstacle의 프로퍼티로 넣어주었더니, 캐릭터의 움직임에 따라 방해물인 방패도 움직이는 코드를 짤 수 있었다.


C나 C++ 처럼 메모리를 직접 건들 수 없다는건 단점처럼 보이기도하고 장점처럼 보이기도 하는 것 같다.

