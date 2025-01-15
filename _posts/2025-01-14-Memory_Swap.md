---
layout: post
current: post
cover: "assets/images/cover/algorithm.png"
navigation: True
title: "Memory Swap"
date: 2025-01-14 02:11:00
tags:
    - [AWS, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CS, ]
---

# Swap Memory


t2.micro 를 사용하면서, npm run build 혹은 npm run start 가 특정 커맨드 라인에서 멈추는 현상이 있었다.


열심히 검색해보니 메모리 부족이라는 얘기가 있었는데, 실제로 고사양의 컴퓨터에 해보았을때 문제없이 돌아갔다.
해결방법을 총 2가지를 찾았는데,

1. 메모리 스왑


```
text
sudo dd if=/dev/zero of=/mnt/swapfile bs=1M count=2048
sudo mkswap /mnt/swapfile
sudo swapon /mnt/swapfile


```



디스크를 메모리처럼 사용하여 임시방편을 메모리를 늘리는 방법이다. 하지만 디스크는 메모리에 비해 속도가 매우 느리므로 성능이 저하될 수 밖에 없다.

1. 다른 사양에서 build 후 가져오기.
이 방법은 사용을 해보진 않았지만, 고사양의 서버가 있다면 사용해볼 수 있겠다. 그렇지만 굳이 이렇게 할 이유가 있나?
