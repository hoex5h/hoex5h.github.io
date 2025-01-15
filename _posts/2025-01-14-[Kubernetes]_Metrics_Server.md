---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] Metrics Server"
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

# [Kubernetes] Metrics Server


쿠버네티스에서는 Metric 이라는 것을 활용해서 Pod 들을 오토스케일링 할 수 있다고 한다. 그렇다면 metric 은 무엇일까?


## Metric 이 뭘까


metric 은 쿠버네티스에서 "어떤 시스템의 성능이나 상태" 정도로 생각하면 될 것 같다. 예를 들어 CPU 사용량, 메모리 사용량, 네트워크/디스크 입출력 등등이 있겠다.


## 스케일링


스케일링에는 수직 스케일링과 수평 스케일링, 2가지 스케일링이 있다.


### 수직 스케일링(Vertical Scaling)


수직 스케일링은 어떤 시스템의 성능(CPU, RAM 등)을 조금 더 많이 배치해서 그 시스템의 성능을 끌어 올리는 것이다.


내가 사용하던 컴퓨터의 램을 16GB 에서 32GB 로 업그레이드하는 것을 수직 스케일링의 예로 들 수 있다.


### 수평 스케일링(Horizontal Scaling)


수평 스케일링은 시스템을 복제해서 여러 시스템을 배치하는 것이다. 내가 사용하던 16GB 의 컴퓨터와 동일한 사양으로 하나 더 준비해서, 컴퓨터를 총 2대 가용하는 것을 수평 스케일링의 예로 들 수 있다.


## HPA(Horizontal Pod Autoscale)


Metric 을 이용해서 오토스케일링 한다는 것은 무엇일까? [공식 문서는 여기!(kubernetes_HPA)](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale/)


쿠버네티스에서는 필요시에 Pod 를 복제하여 수평적으로 스케일링을 할 수 있다.


> 쿠버네티스는 Horizontal Pod Autoscaling을 간헐적으로(intermittently) 실행되는 컨트롤 루프 형태로 구현했다(지속적인 프로세스가 아니다). 실행 주기는 kube-controller-manager의 --horizontal-pod-autoscaler-sync-period 파라미터에 의해 설정된다(기본 주기는 15초이다). 각 주기마다, 컨트롤러 매니저는 각 HorizontalPodAutoscaler 정의에 지정된 메트릭에 대해 리소스 사용률을 질의한다. 컨트롤러 매니저는 scaleTargetRef에 의해 정의된 타겟 리소스를 찾고 나서, 타겟 리소스의 .spec.selector 레이블을 보고 파드를 선택하며, 리소스 메트릭 API(파드 단위 리소스 메트릭 용) 또는 커스텀 메트릭 API(그 외 모든 메트릭 용)로부터 메트릭을 수집한다.


쿠버네티스의 컨트롤러는 일정 주기(기본 15초) 마다 타겟의 파드를 선택해서 HorizontalPodAutoscaler 정의에 지정된 메트릭을 수집한다. 만약 내가 CPU 를 지정해두었다면, 15초마다 너 지금 CPU 얼마나 쓰고 있는지 볼게~ 하고 확인하는 것이다.


파드를 지정할 때, 컨테이너에 필요한 각 리소스(CPU와 메모리 등등)의 양을 선택적으로 지정할 수 있다. 예를 들어 특정 Pod에서 각각의 컨테이너가 최소 CPU 100m 은 써야하고(request), 200m 아래로 썼으면 좋겠어(limit) 라고 지정할 수 있다.


또한 HorizontalPodAutoscaler 에는 최소, 최대 레플리카의 개수를 지정할 수 있다. 예를 들어 아래와 같이 오토스케일러를 지정했다고 하자.



```
yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: web-application-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-application
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50


```



wen-application 이라는 Deployment는 Pod가 최소 2개 있어야 하며, 최대 10개까지 복제가 가능하다.


만약 존재하는 Pod 들의 평균 CPU 사용률이 50퍼센트가 넘어간다면, HPA 는 scale up 을 하게 될 것이다.


이렇게 생성된 Deployment 는 Pod 2개를 미리 생성해놓고 대기하고 있을 것이다. 이때 갑자기 많은 양의 사용자가 몰렸다고 하자.
첫 번째 Pod 의 CPU 사용률은 40퍼센트, 두 번째 Pod 의 CPU 사용률은 70퍼센트가 되었다.
15초마다 HPA는 정기적으로 CPU 사용률을 체크하다가,
"`! CPU의 평균 사용률이 (40+70)/2 = 55% 네!, 50퍼센트를 넘었잖아!" 라고 확인하게 되면
아래의 공식으로 레플리카 수를 측정해서 늘린다.


`원하는 레플리카 수 = ceil[현재 레플리카 수 * ( 현재 메트릭 값 / 원하는 메트릭 값 )]`


> 원하는 레플리카 수 = ceil[2 * (55 / 50)] = ceil(2.xx) = 3


	ceil 은 소숫점 올림 함수


현재 레플리카가 2개 존재 하기 때문에, 1개를 추가 생성해서 3개로 맞추게 된다. HPA는 이렇게 수평적으로 확장(Scale up)만 하는 것이 아니라 부하량이 줄어들고, 파드의 수가 최소 설정값 이상인 경우, HorizontalPodAutoscaler는 워크로드 리소스(디플로이먼트, 스테이트풀셋, 또는 다른 비슷한 리소스)에게 축소(Scale down)을 지시하기도 한다.


## Metrics Server


그렇다면 이 Metric 은 어떻게 수집할 수 있을까? 이 역할을 바로 Metrics Server 가 담당한다. 이제 Metrics Server 를 설치해보자.



```
shell
kubectl apply -f <https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml>


```



사실 Metrics Server 를 설치하고 CA 인증 절차를 거쳐야 하는데 지금은 간단하게 살펴만 보고 추후에 CA 인증을 하도록 하자..



```
shell
kubectl edit deployment metrics-server -n kube-system


```



이후 spec -> container -> args 항목에서

- -kubelet-insecure-tls=true
를 추가한다.

## 참고!


[파드 및 컨테이너 리소스 관리](https://kubernetes.io/ko/docs/concepts/configuration/manage-resources-containers/)

