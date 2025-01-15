---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] kubelet is not running"
date: 2025-01-14 02:40:00
tags:
    - [Kubernetes, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CICD, ]
---

# [Kubernetes] kubelet is not running


쿠버네티스를 1.29 버전으로 업그레이드 하면서 새롭게 kubeadm init 을 했는데, 아래 사진처럼 에러가 났다.


![0](/upload/2025-01-14-[Kubernetes]_kubelet_is_not_running.md/0.png)_image.png_


원인을 알아보려고 우선 `systemctl status kubelet` 과 `journalctl -xeu kubelet` 을 실행해봤는데,


![1](/upload/2025-01-14-[Kubernetes]_kubelet_is_not_running.md/1.png)_image.png_


아무래도 runc 의 문제인 것 같았다.


## containerd, runc 설치에 문제가 있었나?


우선 `which containerd` 와 `which runc` 를 실행해봤더니, containerd 는 문제없이 잘 찾았는데 runc 를 찾지 못했다.
일단은 빠르게 해결하려고 심볼릭 링크를 걸어서 runc 를 찾을 수 있도록 했는데 이유가 뭔가 하고 봤더니..
`usr/local/bin` 에 runc 바이너리 파일이 아니라 runc 폴더 안에 runc 바이너리 파일이 있던 것이었다.


맨날 apt 같은 패키지 매니저를 사용하다가 이번에 처음으로 바이너리 파일로 다운받다보니 많이 헤매고 있다..


runc를 정상적으로 설치한 후에는 systemctl status kubelet 으로 kubelet 상태가 정상인 것도 확인할 수 있고, kubeadm init 도 정상적으로 동작하는 걸 확인할 수 있다!

