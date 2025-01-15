---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] Install Metallb"
date: 2025-01-14 02:39:00
tags:
    - [Kubernetes, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [CICD, ]
---

# [Kubernetes] Install Metallb


Metallb 를 설치하기 전에, k8s 버전에 따라 kube-proxy 버전을 확인하는 것이 좋다.


`kubectl get configmap kube-proxy -n kube-system -o yaml | grep mode`


위의 커맨드의 결과가 `ipvs` 라면, `strictARP` 옵션을 True 로 설정해야한다고 한다.



```
text
kubectl get configmap kube-proxy -n kube-system -o yaml | \\
sed -e "s/strictARP: false/strictARP: true/" | \\
kubectl apply -f - -n kube-system


```



이후, Metallb 를 설치하자



```
text
kubectl apply -f <https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml>
kubectl apply -f <https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml>


```



이후 Metallb 에서 사용할 ip 대역을 설정해줘야하는데, 여기서 `layer 2` 모드와, `BGP` 모드를 사용할 수 있다고 한다.


calico 가 BGP 를 이용하여 트래픽을 라우팅한다고 언뜻 들었던 것 같은데, 우선은 layer 2 모드를 사용해보자.


`metallb_config.yaml`



```
yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - ${IP 대역폭}


```




```
text
kubectl apply -f metallb_config.yaml


```



이제 service 의 type 을 LoadBalancer 로 만들었을 때, external-ip 가 설정했던 대역폭으로 잘 설정된다면,


Metallb 설치가 정상적으로 완료된 것이다.

