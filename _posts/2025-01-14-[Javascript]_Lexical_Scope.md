---
layout: post
current: post
cover: "assets/images/cover/javascript.png"
navigation: True
title: "[Javascript] Lexical Scope"
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

# [Javascript] Lexical Scope


## 문제의 발단


자바스크립트로 아이작과 비슷한 게임을 만들던 도중 화살표함수에서 $(this) 사용이 이상하다고 느꼈는데, 아래와 같은 코드를 짜고 실행시켰더니



```
javascript
$(`#character`).fadeOut(2000, "swing", ()=>{
  console.log($(this)); // 이 부분에서 $(this)를 콘솔에 찍어보았더니
});


```




```
javascript
  $(window)


```



윈도우 객체가 콘솔에 찍혔다.


그저 function(){} 을 간단히 쓰는 느낌은 아닌것이었다..


화살표 함수에 대해 알아보기전에, Lexical Scope 를 먼저 알아야 할 것 같아서 화살표 함수는 이 뒤에 다루기로 하고, 먼저 렉시컬 스코프 (또는 Static Scope)에 대해 먼저 알아보자.


### 결론부터 미리!


미리 얘기해두자면, 자바스크립트는 기본적으로 Lexical Scope 를 따른다.


## Lexical Scope 정의


> Lexical Scope(혹은 Static Scope) : 함수를 어디서 선언하였는지에 따라 상위 스코프를 결정한다


## Scope 정의


> Scope : 참조 대상 식별자(변수,함수의 이름과 같이 어떤 대상을 다른 대상과 구분하여 식별할 수 있는 유일한 이름)를 찾아내는 규칙.  
> 함수가 선언될 때 Scope가 생긴다!


## Sample Code



```
javascript
var str = '1';
var text = 'global';

function consoleLog1(){
    console.log(str);
}

function upperScope(){
    var str = '2';
    consoleLog1();

    function consoleLog2(){
        console.log(str);
        console.log(text);
    }

    consoleLog2();
}


upperScope();


```



### 코드 설명


위의 코드를 설명해보자면, 전역변수 str 은 '1' 로 선언되어 있다. str을 출력하는 함수는 consoleLog1과 consoleLog2가 있는데, consoleLog2 함수는 upperScope 라는 함수 안에 있다.


두 번째 전역변수 text를 보면, upperScope 함수 안에 있는 consoleLog2 에서 출력하게 되어 있다.


우선 위에도 써놨듯이, Scope는 함수가 호출될 때가 아니라 선언될 때 생긴다. 라는 걸 머릿 속에 넣어두고 넘어가자.


자바스크립트가 Lexical Scope 를 따른다고 말한건, 함수가 선언 되었을 때 우선 탐색하는 스코프는 상위스코프 라는 소리다.


consoleLog1 함수를 보자.


선언되는 상황을 보면 상위 스코프에 (전역변수) str(1) 이 존재한다. 따라서 consoleLog1 이 upperScope 에서 실행된다고 한들, 전역변수인 str(1) 을 참조하는 것이다.


두 번째로 upperScope 안에 있는 consoleLog2 를 보자.


선언 된 순간을 보면 consoleLog2의 바로 상위 스코프인 upperScope 에 존재한다. 따라서 upperScope의 지역변수로 선언된 str(2)를 참조한다.


이번엔 두 번째 전역변수 text를 살펴보자.


consoleLog2에서 text를 출력하려고 보니, 바로 상위스코프인 upperScope 에는 text라는 변수가 없다. 혹시 그럼 undefined를 출력할까?


아니다. 바로 위 스코프에 없다면, 그 위에 스코프를 탐색하고, 해당 변수를 찾을때까지 위의 위 스코프를 탐색한다. 그럼에도 찾지 못한다면 undefined가 될 것이다.


text 함수는 전역변수로 선언되어 있기 때문에 upperScope 에서는 찾지 못했어도 전역스코프에서는 찾을 수 있다. 따라서 'global' 이란 텍스트를 출력할 것이다.


## Scope Chain


> 이렇게 꼬리를 물며 위 스코프를 탐색하는 방법을 '스코프 체인' 이라고 한다.


이제 다음 문서에 lexical this 를 설명해보려고 한다.


lexical Scope가 상위 스코프를 참조한다는걸 생각해보면, lexical this 도 마찬가지 아닐까?

