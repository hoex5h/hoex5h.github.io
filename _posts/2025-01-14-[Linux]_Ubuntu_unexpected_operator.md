---
layout: post
current: post
cover: "assets/images/cover/linux.png"
navigation: True
title: "[Linux] Ubuntu unexpected operator"
date: 2025-01-14 02:40:00
tags:
    - [Linux, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Infra, ]
---

# [Linux] Ubuntu unexpected operator


helm 스크립트를 받아와서 설치하는  Ansible playbook yaml 파일을 작성중에, 자꾸 unexpected operator 에러가 발생했다.



```
text
- name: 설치 스크립트 다운로드 및 Helm 3 설치
get_url:
url: [https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3](https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3)
dest: /tmp/get_helm.sh
mode: '0700'  # 실행 권한을 부여합니다.

- name: Helm 3 설치
  shell: sh /tmp/get_helm.sh


```



왜 이런고 하니, ubuntu 에서는 sh 링크가 bash 가 아니라 dash 에 걸려있다.


`sh get_helm.sh`


이런식으로 "sh" 를 앞에 적어줬더니 dash 는 get_helm.sh 를 이해를 못한것이었다


해결책으로는 그냥 sh 커맨드를 떼버리거나,
`get_helm.sh`


아니면 앞에 bash 라고 명시를 해주는 방법이 있다.
`bash get_helm.sh`


아니면, 앞으로 sh 는 bash 로 링크되도록 하는 방법도 있다.



```
shell
sudo unlink /bin/sh
sudo ln -s /bin/bash /bin/sh
sh ysoftman.sh


```


