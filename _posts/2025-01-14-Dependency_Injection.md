---
layout: post
current: post
cover: "assets/images/cover/nestjs.png"
navigation: True
title: "Dependency Injection"
date: 2025-01-14 02:40:00
tags:
    - [TypeScript, NestJS, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

Dependency Injection ( Angular 공식 문서 )


[nest.js 공식문서](https://docs.nestjs.com/providers)를 읽어보는 도중 DI 관련한 문서에 대한 링크로 [Angular 의 공식 문서](https://www.angular.kr/guide/dependency-injection)를 걸려있었다.


자세하게, 한국어로도 적혀있는 것 같아서 nestjs 를 쓰기 전에 꼼꼼히 읽어보려고 한다.


# 의존성 주입은 왜 사용할까?


> Dependency Injection, or DI, is a design pattern and mechanism for creating and delivering some parts of an application to other parts of an application that require them.


의존성 주입은 `애플리케이션의 일부(A)`를 `또 다른 곳(B)`에서 필요로 할 때, A 를 만들고 전달하는 역할을 한다.


즉 다른 클래스에 있는 기능을 사용하고 싶을 때 의존성을 주입해주는 것이라고 생각하면 된다.


# 의존성을 사용하는 쪽, 제공하는 쪽


단순하게 의존성을 주입 하는 것은 어떤 방식으로든 하나의 객체에 다른 클래스의 객체를 넘기기만 해도 의존성 주입이라고 부를 수 있다.



```
typescript
class Foo {
    constructor(){
        // ...
    }
}

class UsingDI {
    constructor(private injectClass)
}

class DoNotUseDI {
    constructor(){
        this.injectClass = new Foo();
    }
}



```



위의 예시대로라면 `DoNotUseDI` 객체는 직접 Foo 객체를 생성해서 사용한다.


따라서 아주 강한 결합을 가지게 되는데, 만약 나중에 Foo 가 아니라 Bar 라는 클래스로 바꾸고 싶다고 하면
`DoNotUseDI` 클래스의 코드도 수정해야하고 테스트할 때도 꼭 Foo 객체를 넣어줘야 하기 때문에 복잡해지게 될 것이다.


반면 `UsingDI` 클래스는 생성자로 어떤 객체를 넘기느냐에 따라, 그 객체에 결합이 생긴다. 위의 예시에 비해 더 동적으로 의존성이 생긴다고 볼 수 있다.


따라서 Foo 의존성을 주입했다가, 나중에는 Bar 객체를 주입할 수도 있고, 또 나중에는 어떤 객체라도 덕타이핑에 문제가 없다면 (일단은) 주입할 수 있을 것이다.


또한 테스트할 때도 굳이 실제 객체를 넣어줄 필요 없이 간단한 모킹 객체를 넣어줄 수도 있을 것이다.


다만 여기서 기억해두어야 할 점은 의존성은 사용하는 쪽(주입 당하는 쪽) 과 제공하는 쪽(주입하는 쪽) 이 있다는 것이다.


# 의존성 주입


## Injector


`injector` 객체는, 미리 생성된 프로바이더(의존성을 제공당할 수 있는 클래스) 대로 의존성 객체를 생성하고 캐싱해둔다.


또한 인젝터는 (기본적으로) 의존성 객체들을 싱글톤 인스턴스로 관리한다.


## 모듈에 따라?


NestJS 의 컴포넌트는 크게 `모듈`, `프로바이더`, `컨트롤러`, `서비스` 등으로 계층을 나눌 수 있다.



```
typescript
@Module({
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}

@Module({
  imports: [CatsModule],
  controllers: [DogsController],
})
export class DogsModule {}



```



위의 예시처럼 NestJS 모듈 별로 프로바이더, 서비스, 컨트롤러 등등을 주입받는데 모듈별로 injector 가 생성되고 해당 인젝터가 의존성 객체들을 캐싱하고 있다.


또한 CatsModule 에 주입된 CatsService 를 export 함으로써 DogsModule 에서 CatsModule 의 프로바이더를 가져와서 사용할 수 있게 된다.


## @Injectable : 의존성 주입 가능



```
javascript
@Injectable
class CatService {}


```



클래스를 의존성으로 주입하려면 가장 먼저 `@Injectable` 데코레이터를 사용해야한다.


## @Module : 의존성 등록


NestJs 는 `@Module` 로 의존성을 등록해줄 수 있다.


## 프로바이더 Scope


의존성 객체가 되는 프로바이더의 생명 주기는 애플리케이션의 생명 주기와 같다. 애플리케이션이 부트스트랩 될 때, 모든 의존성은 해결(사용 가능)이 되어야 한다.
그렇기 때문에 애플리케이션이 부트스트랩 될 때, 모든 프로바이더들이 인스턴트화 되어야 하고 애플리케이션이 종료될 때 모든 프로바이더들은 제거된다.


만약 프로바이더의 Scope 를 직접 정하고 싶다면, [request-scoped](https://docs.nestjs.com/fundamentals/injection-scopes) 의 방법을 사용할 수 있다고도 한다.

