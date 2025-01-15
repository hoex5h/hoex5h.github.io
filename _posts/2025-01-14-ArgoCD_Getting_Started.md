---
layout: post
current: post
cover: "assets/images/cover/argocd.png"
navigation: True
title: "ArgoCD Getting Started"
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

# [Argocd] Getting Started


[Argocd 공식문서](https://argo-cd.readthedocs.io/en/stable/getting_started/)


## Install Argo CD


졸업프로젝트로 k8s 를 써보면서, CD tool 로 Argocd 를 사용해보려고 한다.


공식문서를 참고해서 설치해보자.



```
text
kubectl create namespace argocd
kubectl apply -n argocd -f <https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml>


```



설치가 잘 되었는지 확인해보자.



```
text
kubectl get all -n argocd


```



<details>
<summary> 결과 </summary>
<code>
<pre>
...
pod/argocd-redis-774dbf45f8-mvw4n                       1/1     Running   0          4m56s
pod/argocd-repo-server-76d7968f94-j99cw                 1/1     Running   0          4m56s
pod/argocd-server-5db7487c98-ljqhr                      1/1     Running   0          4m56s
...


NAME                                              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/argocd-server                             ClusterIP   10.96.132.227    none        80/TCP,443/TCP               4m57s
...


</pre>
</code>
</details>


여기서 우리가 봐야할 것은 `service/argocd-server` 이다.


TYPE이 ClusterIP 라서 External-IP도 없고, 외부로 expose 도 되어 있지 않다.


공식 문서에서는 타입을 Load Balancer 로 바꾸거나, Ingress 설정을 하거나, 포트 포워딩을 하라고 하고 있다.


여기선 제일 간단하게 타입을 로드 밸런서로 바꿔보자.


`kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'`


혹시 여기서, External ip 가 pending 되고 있다면 on-premise 환경에서 사용할 loadbalancer 가 필요하므로, MetalLB 같은 것을 설치해야한다.


[Install Metallb](https://www.notion.so/InstallMetallb.html)


## Argocd CLI 설치



```
text
cd /usr/local/bin
sudo curl -sSL -o argocd-linux-amd64 <https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64>
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
sudo rm argocd-linux-amd64


```



## Argocd 초기 비밀번호 확인



```
text
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo


```



argocd cli가 깔려있다면, 아래의 커맨드로도 확인할 수 있다.



```
text
argocd admin initial-password -n argocd


```



## Argocd 로그인 및 비밀번호 변경



```
text
argocd login <ARGOCD_SERVER ip>
argocd account update-password


```



username = admin


passwd = 위에서 설명했던 초기 비밀번호


로 로그인 하면 된다.

