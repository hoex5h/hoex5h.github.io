---
layout: post
current: post
cover: "assets/images/cover/effective-typescript.png"
navigation: True
title: "Effective TypeScript - 2"
date: 2025-01-14 03:54:00
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


# 타입스크립트 컴파일러 역할

1. 구 버전 브라우저를 위한 트랜스파일 -> Babel 의 역할이었던거 같은데?
2. 코드의 타입 오류 체크

두 역할은 서로 `독립적`이다. 서로의 작업에 영향을 주지 않는다.


## 타입 오류가 있어도 컴파일이 가능


컴파일은 타입 체크와 독립적으로 동작하므로 오류 있어도 컴파일은 가능


만약 오류가 있을 때 컴파일 하고 싶지 않으면 tsconfig.json 에 `noEmitError` 옵션을 true 로 주면 된다.


## 런타임에는 타입 체크 불가능


타입스크립트에서 자바스크립트로 컴파일되면서 타입이나 인터페이스 같은 경우 전부 제거된다.
즉, 런타임 시점에서 타입과 인터페이스는 아무런 역할을 하지 못한다.


## 함수 오버로딩 불가능


선언문은 타입 별로 여러개 선언은 가능하다. 하지만 구현체는 오직 하나다.


## 덕타이핑


[interface 와 duck-typing](https://hoeeeeeh.github.io/javascript/2024/07/29/day1112.html#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-interface-duck-typing)


# Any 타입, 꼭 써야겠어?

- any 타입 쓰면 타입에 안전성이 없다. 쓰면 number 타입에 string 이 할당될 수도 있어
- any 타입 쓰면 함수의 시그니처(약속/의도 정도로 해석?)가 무시된다.
- any 타입 쓰면 자동완성 기능, 코드 리팩토링 도구 쓰기 힘들어져 -> 생산성 떨어진다!
- any 타입 쓰면 코드 리팩토링할 때 버그를 찾기 힘들어
- any 타입 쓰면 타입 설계의 의도, 상태 등이 감춰져서 설계를 해석하기 힘들어
- any 타입 쓰면 실수가 빈번해져

이래도 any 타입, 꼭 사용하셔야겠습니까?


근데 불가피하게 any 타입을 꼭 써야할 때가 있다. 이는 5장에서 다룬다.

