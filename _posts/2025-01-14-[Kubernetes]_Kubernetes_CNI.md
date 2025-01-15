---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] Kubernetes CNI"
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

# [Kubernetes] Kubernetes CNI


## 쿠버네티스 클러스터 네트워킹의 두 가지 원칙


[쿠버네티스의 공식 문서](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/)를 참조해서 CNI 가 무엇인지, 어떠한 역할을 하는지 알아보자. 참고로 한국어로 번역된 페이지도 생각보다 많으니 공식 문서에서 한국어가 지원되는 페이지인지 확인해보는 것도 좋다.


쿠버네티스 클러스터의 네트워킹에서는 반드시 지켜야 하는 두 가지 원칙이 있다.

1. 임의의 노드에 있는 파드와, 다른 모든 노드에 있는 모든 파드들과 물리적으로 통신할 수 있어야 한다.
2. 임의의 노드에 있는 파드와, 같은 노드에 있는 모든 파드들과 물리적으로 통신할 수 있어야 한다.

2번의 경우, 같은 노드에 있는 파드들은 같은 네임스페이스를 공유하기 때문에 로컬 호스트로 각각 컨테이너에 할당된 포트로 통신이 가능하다.


같은 노드에 있는 A pod(8080) 와 B pod(7070) 은 서로 로컬호스트 포트로 통신이 가능하다는 뜻이다.


그렇다면 1번 원칙은 어떻게 될까?


뜬금없지만 사실 쿠버네티스 내부에는 네트워크가 구현되어 있지 않다. 위에서 언급했던 원칙처럼 어떻게 통신이 이루어져야 하는지에 대한 사양만 있고, 직접 구현되어 있지는 않다.


사용자가 자신의 환경을 고려해서 적절한 네트워크 플러그인을 가져다 써야하는데, 이때 사용하는 네트워크 스펙 인터페이스가 CNI 이다.


## CNI가 뭘까?


[CNI Github](https://github.com/containernetworking/cni)


CNI 공식 깃허브 문서에 따르면, `CNI(Container Network Interface)는 리눅스 컨테이너에서 네트워크 인터페이스를 구성하기 위한 플러그인을 작성하는 사양(specification)과 라이브러리로 구성된 프로젝트이다. CNI는 오직 컨테이너간 네트워크 연결과 컨테이너가 삭제될 때, 할당된 리소스들을 지우는 것에만 관여한다. 이러한 점들 때문에, CNI는 넓은 범위의 지원을 받으며 구현하기 간단한 사양을 가지고 있다.`


CNI는 리눅스 컨테이너의 네트워크 인터페이스를 관리할 수 있는 사양(specification)에 대한 프로젝트다. 컨테이너가 생성되고 삭제될 때, 네트워크 인터페이스가 생성되고 삭제되는 것을 어떻게 처리할 것인지에 대한 내용을 포함하고 있다.


## CNI는 그럼 왜 필요할까?


네트워크에 대한 구성은 인프라 환경에 따라서 구성이 매우 복잡하고 다양하다. 이렇게 다양한 네트워크 환경에서 수 많은 문제점들이 발생할 수 있는데, 이 수많은 문제점들에 대한 해결 방법도 천차만별이다. 결국 환경에 따라 네트워크 구성이 점점 복잡해지고 획일화되기는 쉽지 않게 될 것이다.


CNI는 이렇게 네트워크 구성과 문제점이 복잡해지는 것을 막기 위해서 등장했다. 컨테이너 네트워크와 관련된 표준 스펙을 정의하고 있는 CNI를 통해서 네트워크를 구성하게 된다면 복잡했던 네트워크 구성과 문제점들을 어느정도 일축시킬 수 있을 것이다.


## Kubernetes CNI


그 중에서 쿠버네티스를 위한 대표적인 플러그인들에는 `Calico, Flannel, Cilium ...` 등이 있다. 더 자세한 내용은 [쿠버네티스 네트워킹 애드온](https://kubernetes.io/ko/docs/concepts/cluster-administration/addons/#network-and-networking-policy) 을 참고하자.


## Calico


우리는 이번 기회에 Calico CNI 를 사용해볼 예정이기 때문에, Calico 에 대해 조금 자세히 알아보자.


[What is Calico?](https://docs.tigera.io/calico/latest/about/)


버전이 올라가면서 Calico 공식문서 및 깃허브 링크가 바뀌는 경우가 있다. 기존의 프로젝트에서 떼어져 나온다든지, 이러한 이유들이 있는걸로 보인다.


### What is Calico


> 쿠버네티스에서는 기본적으로 파드 간 통신에 대한 기본 설정은 모두 허용(default-allow)이다. 네트워크 정책을 이용해서 이를 제한하지 않는다면, 모든 파드들은 다른 파드들과 제약없이 통신할 수 있다.


Calico 는 네트워크 통신을 보호하는 네트워킹과 cloud-native 의 마이크로서비스/애플리케이션을 보호하기 위한 고급 네트워크 정책으로 구성되어 있다.


Calico CNI는 containers, Kubernetes clusters, virtual machines, and native host-based workloads 를 보호하는 L3/L4 네트워킹 솔루션으로,  여러 데이터플레인을 프로그래밍하는 컨트롤 플레인이다.


> 데이터 플레인 : 데이터 패킷이 이동하는 부분으로 실제로 사용자 또는 시스템 간에 데이터를 전송하는 역할을 담당하는 플레인. 데이터 패킷을 처리하고, 패킷의 다음 목적지로 전달하는 실질적인 처리와 전송을 담당하는 플레인 (근육)


> 컨트롤 플레인 : 네트워크 경로를 결정하고, 정책을 설정하는 등의 네트워크 운영을 총괄하는 플레인. 네트워킹 장비에게 어떤 경로로 데이터 패킷을 전달해야 하는지 라우팅을 생성하고 관리하는 기능을 담당하는 플레인 (손,발)


![0](/upload/2025-01-14-[Kubernetes]_Kubernetes_CNI.md/0.png)_image.png_

