---
layout: post
current: post
cover: "assets/images/cover/flutter.png"
navigation: True
title: "[Flutter] VerifyReleaseResources Error"
date: 2025-01-14 02:14:00
tags:
    - [Flutter, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Programming, ]
---

# [Flutter] VerifyReleaseResources Error


Flutter 를 활용한 채팅(상담) 어플을 만들고 있었는데, APK 빌드를 하려고 하니까 아래와 같은 오류가 발생했다.


($ flutter build apk --release)



```
text
FAILURE: Build completed with 2 failures.

1: Task failed with an exception.
-----------
* What went wrong:
Execution failed for task ':keyboard_visibility:verifyReleaseResources'.
> A failure occurred while executing com.android.build.gradle.internal.tasks.Workers$ActionFacade
   > Android resource linking failed
     /Users/hoeeeeeh/.gradle/caches/transforms-2/files-2.1/c82bf40584b9a53c31dba07a8a48f301/core-1.1.0/res/values/values.xml:142:5-173:25: AAPT: error: resource android:attr/fontVariationSettings not found.

     /Users/hoeeeeeh/.gradle/caches/transforms-2/files-2.1/c82bf40584b9a53c31dba07a8a48f301/core-1.1.0/res/values/values.xml:142:5-173:25: AAPT: error: resource android:attr/ttcIndex not found.


* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.
==============================================================================

2: Task failed with an exception.
-----------
* What went wrong:
Execution failed for task ':safe_area_height:verifyReleaseResources'.
> A failure occurred while executing com.android.build.gradle.internal.tasks.Workers$ActionFacade
   > Android resource linking failed
     /Users/hoeeeeeh/.gradle/caches/transforms-2/files-2.1/c82bf40584b9a53c31dba07a8a48f301/core-1.1.0/res/values/values.xml:142:5-173:25: AAPT: error: resource android:attr/fontVariationSettings not found.

     /Users/hoeeeeeh/.gradle/caches/transforms-2/files-2.1/c82bf40584b9a53c31dba07a8a48f301/core-1.1.0/res/values/values.xml:142:5-173:25: AAPT: error: resource android:attr/ttcIndex not found.


* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.
==============================================================================


```



keyboard_visibility 패키지와 safe_area_height 패키지에서 verifyReleaseResources 를 실패했다고 하는데, android/build.gradle 에 아래와 같은 코드를 넣어서 해결했다.


> android/build.gradle



```
dart
subprojects {
    afterEvaluate { project ->
        if (project.name == "keyboard_visibility" || project.name == "safe_area_height") {
            android {
                compileSdkVersion 28
            }
        }
    }
}


```



gradle build lifecycle 중에 afterEvaluate 에서 사용할 버전을 맞추는 것이라고 한다.

