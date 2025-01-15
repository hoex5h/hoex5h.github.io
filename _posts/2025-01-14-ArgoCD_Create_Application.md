---
layout: post
current: post
cover: "assets/images/cover/argocd.png"
navigation: True
title: "ArgoCD Create Application"
date: 2025-01-14 02:14:00
tags:
    - [ArgoCD, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CICD, ]
---

# [Argocd] Create Application


## Create Application Error


어플리케이션 등록하면서 겪었던 에러들 위주로 작성해보려고 한다. 추후 option 들에 대해서도 공부하고 작성해야겠다.


### PATH absolute error


Path 에 / 를 입력하면 발생하는 에러이다. 절대경로로 설정하면 안되는 것 같다.


### Permission Denied


현재 사용중인 account 에 권한이 없을 때 나타나는 에러이다.


[argocd RBAC 권한](https://www.notion.so/ArgocdRBAC.html) 을 참조해서 권한 설정을 하자

- -2023-09-26 추가

Application 을 생성할 때 Project Name 을 선택하게 되는데, 정말 IDE 열어서 프로젝트 이름 짓는거 마냥 지어버리면 Permission Denied 가 뜬다...!!


Argocd 에 Project 라는 항목이 존재 하는데, 이 항목을 고르는거라서 존재하지 않는 Project Name 을 써버리면 Permission Denied 로 튕겨내버린다..


RBAC로 아무리 권한을 부여해도 뜬다.


에러를 조금만 더 친절하게 써줬으면 좋지 않았을까? 하루종일 RBAC 문제인 줄 알았다.


### Repo URL error


없는 레포나, private 레포일 경우 발생한다.


private 레포의 경우 따로 credential 이 필요하다.


### Failed to unmarshal jsconfig.json:


k8s 와 관련 없는 manifest 들을 읽으려고 할 때 발생하는 문제이다.
SOURCE 의 repo 와 path 를 잘 지정해서, k8s 에 필요한 yaml 파일들만 읽도록 하면 된다.
즉, k8s 에서 필요한 pod, deploy, svc.. 등등의 yaml 을 한 폴더에 넣어두고, 해당 폴더의 경로를 Path 에 지정해주면 된다.

