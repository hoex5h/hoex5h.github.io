---
layout: post
current: post
cover: "assets/images/cover/swift.png"
navigation: True
title: "[Swift] Undefined symbol: "
date: 2025-01-14 02:40:00
tags:
    - [Swift, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

# Undefined symbol: _OBJC_CLASS_$_ [ ]


네이버 맵 API를 적용시키려고 하는데 Xcode가 계속 Undefined symbol: _OBJC_CLASS_$_ [ ] 에러 메세지만 뿜어댔다.
분명 git-lfs 로 설치도 했고, podfile 에 NMapsMap 도 잘 넣어놨는데 해당 에러 메세지가 계속 발생했다.


열심히 찾아본 결과 git-lfs 를 이용하여 정상적으로 설치했다면 프레임워크의 용량이 약 240MB 가 된다고 하는데, 내 폴더의 용량은 3.3MB 였다.


> 이유는 git-lfs 를 설치하고 초기화를 해주지 않아서였다..



```
text
brew install git-lfs
cd [project_path]
git-lfs install
pod cache clean NMapsMap



```



여기서 git-lfs install 커맨드를 통해 git-lfs 를 초기화 시켜주어야 정상적으로 설치를 할 수 있다고 한다.
앞서, 이것저것 설치해보면서 깔렸을 NMapsMap 이랑 Pods 폴더를 pod cache clean 으로 없애주자.

