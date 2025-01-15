---
layout: post
current: post
cover: "assets/images/cover/kuploy.png"
navigation: True
title: "[Kuploy Project] Kuploy History"
date: 2025-01-14 02:40:00
tags:
    - [Kubernetes, Ansible, ArgoCD, Docker, Linux, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Project, ]
---

# [Kuploy] Kuploy History


CICD 자동화 프로젝트를 만들면서 수정사항이 정말 아주 많이 생기고 있는데,


History 를 보기 좋게 작성해 두는 것이 추후 프로젝트를 리팩토링할 때 아주 쓸모 있을 것 같다.


살짝 늦은 감이 없지 않지만,,


## Kubernetes


### Kompose


Docker-compose 파일을 k8s 에 apply 시키기 위한 tool 이다.


현재 버전은 1.31.0 사용 중이다.


### Kompose 문제점

1. -controller statefulset

	statefulset 을 만들기 위해서 flag 를 썼더니 json 마지막 부분에 status: 항목이 생겨있다.


	status 는 쿠버네티스가 배포/운영 하면서 쓰는 항목이지 apply 하기 전에 쓰는 항목이 아닐텐데 이게 왜 생기지?


	심지어 status: replica : 0 으로 잡혀있어서, 만들자마자 replica 가 1이 되기 때문에 Live Manifest 와 Desired Manifest 가 달라져서 바로 out of sync 상태가 된다...

2. -namespace={"namespace"}
모든 항목의 네임스페이스를 지정해주려고 하니 namespace.yaml 파일을 만들어버린다.

	이대로 배포하면 namespace.yaml 이 apply 되면서 배포한 어플리케이션의 일부가 되어버리는데,


	어플리케이션을 삭제하면 namespace 도 삭제된다..


### Metrics Server


K8s 에 배포되어있는 리소스들의 메트릭을 관찰할 수 있다.


### CNI (Calico)


K8s 의 노드들이 통신하는 규약


여러가지 종류가 있지만 현재 Calico 사용 중


### Storage Class


Persisten Volume Claim 을 만들 때, Storage Class 를 사용하는데


Storage Class 에는 `provisioner`, `parameters`, `reclaimPolicy` 를 정의할 수 있다.


Storage Class 에 대한 자세한 설명은 [여기](추후 작성 예정)를 참고하면 된다.


Kuploy는 로컬에 저장할 수 있으면서 동시에 동적 프로비저닝을 할 수 있어야 한다.


이러한 조건을 만족시켜주는 Storage Class 가 [Rancher/local-path-provisioner](https://github.com/rancher/local-path-provisioner) 였다.


현재는 다른 storage class를 사용할 필요가 없기 때문에 default storage class 이다. (따로 storage class 가 지정되지 않으면 자동으로 default storage class 로 지정)


## Docker


### Docker hub


유저들의 이미지를 저장할 레지스트리를 on-premise 로 만들 여력이,, 안되어서 도커 허브를 쓰기로 했다


나중에 저장소가 많이 늘어난다면 사설 레지스트리를 써보거나, Harbor 혹은 Nexus 를 써볼 수도?


## Github Action


## Argocd


### Installation


### Argocd Api


Kuploy 웹에 사용자가 어떤 어플리케이션을 배포중인지 보여주기 위해서 사용했다.


### Argocd Image Updater


사용자들이 소스코드의 변경 -> image push 하는 과정만 존재할 경우,


즉 현재 argocd 가 호시탐탐 지켜보고 있는 application 의 status 에 변화를 불러 일으킬만한 변경점이 없다면 sync 되지 않는다.


우리는 image version 이 바뀌는 것도 sync 를 해야한다.


### Trouble Shooting


### 절대 Project Name 을 멋대로 짓지 마.


Project Name 을 IDE 에서 프로젝트 생성할 때 짓는 이름 쯤으로 생각하면 무한 오류에 빠진다.


이거 때문에 교수님 앞에서 눈물의 에러쇼를 했다.


> 당신이 원하는 프로젝트 네임을 적는 것이 아니라 ArgoCD에 미리 생성한 Project 중에 하나를 고르는 것이다!!!


	기본으로 default 프로젝트가 존재하고, 따로 원하면 만들 수 있다.

