---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] Node Not Ready"
date: 2025-01-14 02:23:00
tags:
    - [Kubernetes, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CICD, ]
---

# [Kubernetes] Node Not Ready


컴퓨터 3대를 각각 Master 노드 1개와 Worker node 2개로 구성하고 클러스터를 만들어서 Join 시켜놓고 Calico를 설치해서 CNI 까지 설정을 완료하고 사용하고 있던 어느날,


컴퓨터를 한 번씩 재부팅 해야하는 일이 생겨서 재부팅 했었다.


재부팅하고 나서 보니까 Worker Node 들이 전부 Not Ready 가 되어있었다...


Calico 도 이미 설치를 해놔서 CNI 설정 문제도 아니였는데 뭐가 문제였을까 하고 보니,


Worker Node 들의 swap memory 가 켜져 있었다..


이런일이 없도록 앞으로 추가할 Worker Node 들은 재부팅 후 swap memory 가 켜지지 않는 방향으로 ansible playbook 을 작성해야겠다.

