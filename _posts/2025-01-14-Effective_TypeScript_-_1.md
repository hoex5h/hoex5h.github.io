---
layout: post
current: post
cover: "assets/images/cover/effective-typescript.png"
navigation: True
title: "Effective TypeScript - 1"
date: 2025-01-14 03:52:00
tags:
    - [TypeScript, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

> 이펙티브 타입스크립트 책과  
> 이펙티브 타입스크립트 스터디 영상을 공부하며 정리한 내용입니다.


# TS, JS


`타입스크립트는 자바스크립트의 상위 집합` 이다. `타입스크립트는 타입이 정의된 자바스크립트의 상위 집합이다.`


모든 JS 프로그램이 TS 라는 명제는 참, 반대는 성립하지 않는다.


# TS 타입체커



```
typescript
// ts
let sample = "this is a sample";
console.log(sample.uppercase());
// uppercase 라는 메소드가 string 형식에 없다!
// toUpperCase() 를 사용하시겠습니까?


```



이런식으로 sample 이 string 이라는걸 명시해주지 않아도 타입스크립트는 초기값으로부터 추론한다.


> 코드의 의도가 무엇인지 타입 구문을 통해 타입스크립트에게 알려줄 수 있기 때문에 코드의 동작과 의도가 다른 부분을 찾을 수 있다.


코드의 의도를 표현하려면 명시적으로 선언해라 ex) interface



```
typescript
interface person {
  name: string;
  age: number;
}

const people: Person[] = [
  // Person 예시
  { name: "John", age: 5 },
  //...
];


```



# 자동 형변환 체크?



```
typescript
const a = 2 + "3"; // 자바스크립트 런타임에서 정상, String 타입의 '23'
const b = null + 7; // 자바스크립트 런타임에서 정상, Number 타입의 7, 그러나 typescript 는 오류를 발생시킴
const c = [] + 12; // 자바스크립트 런타임에서 정상, String 타입의 '12', 그러나 typescript 는 오류를 발생시킴
// 근데 이건 왜 String 타입이 되는걸까?
alert("Hi", "world"); // 자바스크립트 런타임에서 정상, 앞의 "Hi" 만 나옴. 하지만 alert는 arg가 0개~1개 인데 2개를 가져왔다고 ts 오류


```



# 모든걸 다 잡아주지는 않는다.



```
text
const a = ["hi"];
console.log(a[2]); // 컴파일은 정상, 런타임에서 오류


```



문법의 엄격함은 온전히 취향의 차이이다.


# 타입스크립트 컴파일러의 셋팅에 따라 바뀐다.


tsconfig.json 파일에서 여러 옵션으로 셋팅을 줄 수 있다. 타입스크립트를 어떻게 사용할 지 동료들에게 알려줄 수 있다.


예를 들어, `noImplicitAny` 옵션은 타입 선언이 없으면 암시적으로 any 타입으로 선언하는 옵션인데, false 와 true 로 옵션을 켜고 끌 수 있다.


`strictNullChecks` 는 모든 타입에 null 과 undefined 를 할당할 수 있는지에 대한 옵션이다.


null 을 명시적으로 허용하려면, `const x : number | null = null;` 처럼 사용하는 것이 좋다. 만약 이렇게 되면 Null check 를 위한 assertion(단언문) 이 필요하다.



```
text
if (x.abc) {
  // null 이 아닐때
}
x!.abc; // x가 Null 이 아니라고 단언한다


```



웬만하면 noImplicit, strictNullChecks 는 설정해두고 시작해라. 안그러면 undefined is not a Object 오류를 계속 만날 것이다.


엄격하게 잡고 싶으면 그냥 `strict` 옵션을 true 로 줘라.

