---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] Kubernetes Lens"
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

# [Kubernetes] Kubernetes Lens


## Lens 란 무엇인가


> Kubernetes Lens 는 클러스터 관리 GUI 툴이다.


쿠버네티스로 프로젝트를 할 때, 클러스터를 관리하는 것은 무조건 kubectl 만을 사용해야 하는 줄 알았다.


근데 우연히 어떤 분께서 작성하신 쿠버네티스 포스트를 읽다가 Kubernetes Lens 라는걸 알게 되었는데,


완전 편해보이는것이 아닌가.. 물론 CLI 도 장점이 있지만 역시 GUI가 깔끔하긴하니까..


## Lens 설치


Lens 설치는 크게 어려울 것이 없다. 아래의 링크에서 운영체제에 맞게 다운로드 받으면 끝이다.


[Lens 공식 홈페이지](https://k8slens.dev/)


## Lens 에 Add Cluster


### VPN ?


이 부분이 사실 조금 고민거리였는데, 애초에 쿠버네티스 클러스터는 대부분 private ip 로 구성되어 있을거고


내가 하고 싶은 것은 클러스터 외부에 있는 컴퓨터(내 맥북)에서 클러스터를 관리하고 싶은 것이다.


그러기 위해선 내 맥북이 클러스터와 같은 네트워크로 들어가거나, 아니면 apiserver 를 외부로 노출시켜야 했다.


아무래도 보안상 apiserver 를 외부로 노출시키는것 보단, vpn 을 쓰는게 좋지 않을까? 라는 생각이 들어서 iptime 공유기 vpn 설정을 따라해보려던 찰나,


내가 구매했던 iptime 공유기 버전이 N104E 였는데 이건 저가형이라서 vpn 기능이 없다고 한다. vpn 해보고 싶었는데..


아무튼 그러해서 apiserver 를 외부로 노출시키는 방법을 선택했다.


### apiserver 에 certSANs 추가


그래서 어떤 것을 해야 하느냐?


apiserver 의 CA(Certificate Authority) 에 외부 ip를 추가하는 것인데,


쉽게 말해서 private 하게 구성된 apiserver 에 외부 도메인 혹은 ip 를 허용해줘야 한다.


certSAN 를 추가하는 방법은 `1. kubeadm init 과 동시에 flag 로 추가하는 방법` 이 있고, `2. 이미 init 이 끝났다면 configMap 을 수정하는 방법` 이 있다.


kubeadm 과 동시에 flag 로 추가하려면, `--apiserver-cert-extra-sans` flag 와 함께 init 하면 된다.


그러나 대부분 Lens 를 사용하려는 사람들은 나처럼 이미 kubeadm init 을 통해서 클러스터를 구성하고 여러 작업을 진행한 사람들일 것이다.


이런 경우에는 init 을 다시하기엔 여태 해왔던 작업들이 너무 아까우니까, configmap 을 수정하고, apiserver 의 CA를 다시 발급하는 과정을 수행하면 됩니다.


### Configmap 수정


`1. 현재 configmap 을 가져온다.`



```
text
$ kubectl get configmap kubeadm-config -n kube-system -o jsonpath='{.data.ClusterConfiguration}' > kubeadm-conf.yaml


```



`2. certSANs 를 추가한다.`


![0](/upload/2025-01-14-[Kubernetes]_Kubernetes_Lens.md/0.png)_image.png_


위의 이미지에서 `your.domain.or.ip` 부분에 추가하고 싶은 도메인이나 ip 를 입력하면 된다.


`3. apiserver CA 다시 발급받기`


Cert 를 다시 발급받기 전에, 우선 기존의 Key 가 있으면 재발급이 되지 않기 때문에 기존의 key 들을 다른 곳으로 옮기거나 삭제해야한다. 혹시 잘못될 수 있으니 다른 곳으로 백업해두는 것을 권장.



```
text
$ cd /etc/kubernetes/pki
$ mkdir temp
$ mv apiserver.* temp


```



이후, 재발급 받으면 된다.



```
text
$ kubeadm init phase certs apiserver --config ~/work/kubeadm-conf.yaml


```



재발급 받은 뒤에는 1번에서 만들어두었던 configmap 에 변경사항을 적용한다.


{your_directory}에는 1번에서 만들어둔 configmap 의 경로.



```
text
kubeadm init phase upload-config kubelet --config /{your_diretory}/kubeadm-conf.yaml


```



### LENS 에서 cluster 추가


![1](/upload/2025-01-14-[Kubernetes]_Kubernetes_Lens.md/1.png)_image.png_


공식 홈페이지에서 다운받은 뒤에, 사진처럼 `Add Cluster` 를 눌러주면 되는데, 단순히 ~/.kube/config 파일을 불러오는게 아니다.


["Add_Cluster](https://docs.k8slens.dev/getting-started/add-cluster/) 이 문서를 참조하라고 하는데, 확인해보면 아래의 커맨드로 현재의 config 를 불러오면 된다.



```
text
kubectl config view --minify --raw


```



이후에, 사진에서 보이는 `server: https://{your.ip.or.domain}:{api-server-port}` 를 위에서 certSANs 에 추가한 도메인이나 ip로 바꿔주면 된다!


![2](/upload/2025-01-14-[Kubernetes]_Kubernetes_Lens.md/2.png)_image.png_


잘 완료되었다면 아래와 같이 클러스터의 상황을 볼 수 있다!


![3](/upload/2025-01-14-[Kubernetes]_Kubernetes_Lens.md/3.png)_image.png_

