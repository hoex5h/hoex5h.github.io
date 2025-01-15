---
layout: post
current: post
cover: "assets/images/cover/swift.png"
navigation: True
title: "[Swift] Cocoa Pod install Error"
date: 2025-01-14 03:52:00
tags:
    - [Swift, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

# Cocoa Pod install Error


## 에러 내용



```
text
You may have encountered a bug in the Ruby interpreter or extension libraries.
Bug reports are welcome.
For details: <https://www.ruby-lang.org/bugreport.html>


```



### 원인


M1 맥북에서 일어나는 버그라고 하는데,, M1이 출시한지 이렇게나 오래 되었는데 아직도 ㅠ


## 해결 방법



```
text
$ sudo arch -x86_64 gem install ffi
$ arch -x86_64 pod install


```



이렇게 해서 해결할 수 있다고 한다.

