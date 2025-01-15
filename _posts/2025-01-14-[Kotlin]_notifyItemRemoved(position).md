---
layout: post
current: post
cover: "assets/images/cover/kotlin.png"
navigation: True
title: "[Kotlin] notifyItemRemoved(position)"
date: 2025-01-14 02:14:00
tags:
    - [Kotlin, Android, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

# notifyItemRemoved(position)


RecyclerView 에서 특정 position 에 해당하는 item 만 제거할 때,



```
kotlin
notifyItemRemoved(position)


```



을 사용하는데, 만약 여러 개의 리스트에서 해당 함수를 사용할 때마다 position 값이 변경되어야 한다면 문제가 발생할 수 있다.



```
kotlin
notifyItemRemoved(0)


```



을 했다면 position 이 0 인 item 은 RecyclerView 에서 삭제될 것이지만 그렇다고 position 이 1이였던 item의 position 이 0이 되지 않는다.
만약 position 의 수정까지 해야한다면



```
kotlin
notifyItemRangeChanged(position, getItemCount() - position);


```



을 통해 position 값을 변경해줄 수 있다.

