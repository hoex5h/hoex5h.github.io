---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] 도커를 기반으로 멀티노드 클러스터를 구축해보자"
date: 2025-01-14 02:23:00
tags:
    - [Docker, Kubernetes, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CICD, ]
---

# NOTE!


먼저, 실습 환경은 M1 pro 맥북 프로임을 알립니다.


## kind 설치


지난번에는 Docker Desktop 에서 쿠버네티스를 사용해봤는데, 그냥 Docker Desktop 를 이용하면 로컬에서는 싱글 노드로 밖에 클러스터를 구성할 수 없다고 한다.


로컬에서 여러 노드를 클러스터로 구성하려면, "kind" 를 사용해야 한다.


m1 맥에서는 간단하게 brew 를 통해서 설치할 수 있다.



```
text
brew install kind


```



## cluster 생성


kind 에서는 간단하게



```
text
kind create cluster
kind delete cluster --name { $NAME }


```



으로 단일 노드 클러스터를 생성 및 삭제할 수 있다고 한다.


그러나 우리가 사용할 목적은 multiple node 로 구성된 클러스터이기 때문에, config yaml 파일을 하나 만들어야한다.


### cluster config file



```
yaml
kind-make-own-cluster-config.yaml

# four node (three workers) cluster config
# "extraPortMappings" maps literally container to the host machine
# On Mac, port 5000 is used by "ControlCE" which related to "AirPlay"
# If you want to use port 5000, turn off the "AirPlay Receive Mode" in mac os Setting
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: kluster
nodes:
- role: control-plane
  labels:
    roles: master
  extraPortMappings:
  - containerPort: 5001
    hostPort: 5001
- role: worker
  labels:
    roles: worker
  extraPortMappings:
  - containerPort: 5002
    hostPort: 5002
- role: worker
  labels:
    roles: worker
  extraPortMappings:
  - containerPort: 5003
    hostPort: 5003
- role: worker
  labels:
    roles: worker
  extraPortMappings:
  - containerPort: 5004
    hostPort: 5004



```



맥에서 5000번 포트는 Airplay 와 연관된 ControlCE 라는 놈이 이미 차지하고 있으므로, 5001번 포트부터 열었다.


만약 5000번 포트로 열고 싶으면 맥 설정에서 "공유 > 시스템 환경설정 > AirPlay 수신 모드 끄기" 를 하면 된다고 한다. (직접 해보지는 않았으니 반드시 확인해보고 할 것!)


나는 굳이 AirPlay 기능을 건들이고 싶지 않기 때문에 5001번 포트부터 열었다.


이후, 터미널에



```
text
kind create cluster --config kind-make-own-cluster-config.yaml


```



로, 위의 yaml 파일대로 클러스터룰 구성하면 된다.


## Result


아래의 사진처럼 도커에 컨테이너가 생기고, 이 컨테이너들을 클러스터로 묶은 것을 알 수 있다.


![0](/upload/2025-01-14-[Kubernetes]_도커를_기반으로_멀티노드_클러스터를_구축해보자.md/0.png)_image.png_


![1](/upload/2025-01-14-[Kubernetes]_도커를_기반으로_멀티노드_클러스터를_구축해보자.md/1.png)_image.png_


![2](/upload/2025-01-14-[Kubernetes]_도커를_기반으로_멀티노드_클러스터를_구축해보자.md/2.png)_image.png_

