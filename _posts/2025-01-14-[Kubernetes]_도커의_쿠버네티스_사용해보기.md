---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] 도커의 쿠버네티스 사용해보기"
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

# 도커의 쿠버네티스를 사용해보자


먼저, 실습 환경은 M1 pro 맥북 프로임을 알립니다.


## 도커, 쿠버네티스


도커에는 자체적으로 쿠버네티스를 지원한다. 아래 사진 처럼 도커에서


> Setting -> Kubernetes -> Enable Kubernetes 를 활성화해주자.


![0](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/0.png)_image.png_


### Node 확인


이후, 터미널에



```
text
kubectl get nodes


```



를 입력해보면


![1](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/1.png)_image.png_


처럼 현재 실행중인 노드를 확인할 수 있는데, 여기서 기존에 GKE 나 Minikube 를 사용한 적이 있다면 Context 가 Docker 가 아니라, GKE 나 Minikube 로 잡혀있을 수도 있다.


만약 kubectl get nodes 명령어가 정상적으로 실행되지 않는다면, 우선은 kubectl version 으로 kubectl 이 설치되어 있는지 확인해보고 잘 깔려있다면



```
text
kubectl config get-contexts

```



를 입력해서 결과를 보자.


나의 경우에는 쿠버네티스 해보겠다고 GKE도 써보고 Minikube 도 써보고 했는데, 그 덕에 여러가지 Context 가 존재했다.


만약 Current 의 "*" 표시가 Docker-desktop 에 붙어있지 않다면, 지금 이 문서에서는 Docker-desktop 을 사용할 것이기 때문에 context 를 바꿔주자.



```
text
kubectl config use-context { NAME }

```



"kubectl config use-context docker-for-desktop" 으로 context 를 변경하고, "kubectl get nodes" 를 다시 입력해보면 정상적으로 node 가 출력될 것이다.


### deployment


다음으로는 간단한 application 을 실행해보자.



```
text
kubectl create deployment hellok8s --image=k8s.gcr.io/echoserver-arm:1.8 --port=8080
kubectl expose deployment hellok8s --type=NodePort

```



M1 맥에서는 echoserver-arm 을 사용해야 이미지를 정상적으로 가져올 수 있다.


이제 expose 된 서비스를 확인하기 위해



```
text
kubectl get services


```



### deployment result


를 입력해보면, 아까 배포한 hellok8s 의 포트가 8080:3{XXXX} 로 되어 있을텐데, localhost:3XXXX 로 접속해보면


![2](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/2.png)_expose_result_


와 같은 결과를 얻을 수 있다.


---


## layout: post
title: "[Kubernetes] 도커의 쿠버네티스를 사용해보자"
author: hoeh
categories: [ kubernetes ]
image: assets/images/kubernetes.png
toc: true


# 도커의 쿠버네티스를 사용해보자


먼저, 실습 환경은 M1 pro 맥북 프로임을 알립니다.


## 도커, 쿠버네티스


도커에는 자체적으로 쿠버네티스를 지원한다. 아래 사진 처럼 도커에서


> Setting -> Kubernetes -> Enable Kubernetes 를 활성화해주자.


![3](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/3.png)_k8s_in_docker_


### Node 확인


이후, 터미널에



```
text
kubectl get nodes


```



를 입력해보면


![4](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/4.png)_kubectl_get_nodes_


처럼 현재 실행중인 노드를 확인할 수 있는데, 여기서 기존에 GKE 나 Minikube 를 사용한 적이 있다면 Context 가 Docker 가 아니라, GKE 나 Minikube 로 잡혀있을 수도 있다.


만약 kubectl get nodes 명령어가 정상적으로 실행되지 않는다면, 우선은 kubectl version 으로 kubectl 이 설치되어 있는지 확인해보고 잘 깔려있다면



```
text
kubectl config get-contexts


```



를 입력해서 결과를 보자.


나의 경우에는 쿠버네티스 해보겠다고 GKE도 써보고 Minikube 도 써보고 했는데, 그 덕에 여러가지 Context 가 존재했다.


만약 Current 의 "*" 표시가 Docker-desktop 에 붙어있지 않다면, 지금 이 문서에서는 Docker-desktop 을 사용할 것이기 때문에 context 를 바꿔주자.



```
text
kubectl config use-context { NAME }


```



"kubectl config use-context docker-for-desktop" 으로 context 를 변경하고, "kubectl get nodes" 를 다시 입력해보면 정상적으로 node 가 출력될 것이다.


### deployment


다음으로는 간단한 application 을 실행해보자.



```
text
kubectl create deployment hellok8s --image=k8s.gcr.io/echoserver-arm:1.8 --port=8080
kubectl expose deployment hellok8s --type=NodePort


```



M1 맥에서는 echoserver-arm 을 사용해야 이미지를 정상적으로 가져올 수 있다.


이제 expose 된 서비스를 확인하기 위해



```
text
kubectl get services


```



### deployment result


를 입력해보면, 아까 배포한 hellok8s 의 포트가 8080:3{XXXX} 로 되어 있을텐데, localhost:3XXXX 로 접속해보면


![5](/upload/2025-01-14-[Kubernetes]_도커의_쿠버네티스_사용해보기.md/5.png)_image.png_


와 같은 결과를 얻을 수 있다.

