---
layout: post
current: post
cover: "assets/images/cover/javascript.png"
navigation: True
title: "[Javascript] Lexical This"
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

# [Javascript] Lexical This


이전 문서 [Lexical_Scope AND Scope Chain](https://www.notion.so/lexical_scope.html) 에서 렉시컬 스코프와 스코프 체인에 대해 알아보았다.


이번엔 내가 겪었던 문제로 돌아가서 이를 해결해보자.



```
javascript
var testNum = 100;

var lexicalTest = {
    testNum : 0,

    thisTest1: function(){
        console.log(this.testNum);
    },

    thisTest2 : ()=>{
        console.log(this.testNum);
    },
};

lexicalTest.thisTest1();
lexicalTest.thisTest2();


```



위의 코드에서 thisTest1 과 thisTest2 는 같은 testNum을 참조할까?


lexicalTest 라는 객체 리터럴의 testNum을 참조하지 않을까? 설마 혹시나 lexical Scope 처럼 상위 스코프를 참조해서 전역변수 testNum을 참조해버리는 건 아닐까?


우선 function으로 선언된 thisTest1 을 일반적인 함수, 화살표함수로 선언된 thisTest2를 화살표함수라고 부르기로 하자.


일반적인 함수와 화살표 함수에서의 this 는 사뭇 다르다. this는 애초에 변수가 아니라 흔히들 _**문맥**_ 이라고 표현한다.


어떤 상황(문맥)에서 쓰였는지에 따라 달라진다는 의미인 듯 하다. 앞으로 this가 바인딩된다는 표현을 할텐데, this를 문맥과 엮는다는 느낌으로 보면 쉬울 것이다.


> 일반적인 함수에서 this와 문맥은 어떻게 엮이는지 살펴보자.


일반적인 함수에서는 두 가지로 나뉘는데,

1. [생성자 함수 / 객체 메소드] : 객체에 바인딩
2. [1을 제외한 모든 함수(내부함수, 콜백함수 ...)] : 전역객체(window)에 바인딩

lexicalTest 라는 객체 리터럴을 생성할 때 일반적인 함수(thisTest1)는 생성자 함수 안에 있는 함수이므로 객체(lexicalTest)에 바인딩 되어 있고 this.testNum은 lexicalTest.testNum과 동일하다.


> 이번엔 화살표 함수를 살펴보자.


이전에 Lexical Scope 와 Lexical this 를 얘기한건 사실 화살표 함수를 얘기하기 위해서 였다.


화살표 함수의 this 는 Lexical this 라고 불린다.  Lexical Scope가 그랬듯이, 이 this도 _**상위 객체를 참조 한다**_ 는 뜻이다.


다만 이번에는 상위 스코프가 아니라 상위 객체 임을 알아둬야 한다. 모든 객체의 최종 상위 객체는 window 객체이고 전역 변수가 있듯이 window 는 전역 객체 이다.



```
javascript
function upperInstance(){
    this.lexical = 0;

    return {
        lexical : 1,

        normalLog : function(){
            console.log(this.lexical);
        },

        arrowLog : ()=>{
            console.log(this.lexical);
        }
    }
}

upperInstance().normalLog();
upperInstance().arrowLog();


```



위 코드에서 normalLog()는 1이, arrowLog()는 0이 나온다는걸 이해하면 Lexical this는 이해된 것이라 생각한다.


lexical Scope가 상위 스코프를 참조한다는걸 생각해보면, lexical this 도 마찬가지 아닐까?

