---
layout: post
current: post
cover: "assets/images/cover/kuploy.png"
navigation: True
title: "[Kuploy Project] Kuploy "
date: 2025-01-14 02:40:00
tags:
    - [Kubernetes, Ansible, ArgoCD, Docker, Linux, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [Project, ]
---

# [Kuploy] Kuploy 는 어떤 프로젝트인가?


> Kuploy 는 Konkuk + Deploy 의 합성어로 건국대학교 학생들의 Deploy 를 도와주기 위한 프로젝트이다.


## 유휴 컴퓨팅 리소스를 활용해서 학생들에게 제공해주자!


처음 시작은 건국대학교의 유휴 컴퓨팅 리소스들을 활용하여 학생들에게 제공해주자! 라는 취지를 가진 프로젝트였다. 이게 무슨말인가 하면,


건국대학교에는 수 많은 컴퓨터들이 학생들의 수업, 실습을 위해 존재한다. 하지만 실제로 이 컴퓨터들은 수업 시간 외에는 잘 사용되지 않는다.


실습용 컴퓨터도 마찬가지인데, 요새는 대부분 노트북을 하나씩 소유하고 있다보니 모여서 프로젝트를 하는 팀플의 경우를 제외하고는 잘 사용되지 않는다. 이런 상황이다보니 많은 컴퓨터들이 사용되지도 않은 채 관리 미흡으로 먼지가 쌓여가고 있었다.


이렇게 잘 사용되지 않는, `유휴 컴퓨팅 리소스들을 잘 합쳐서 학생들에게 사용할 수 있게끔 제공`한다면 학교와 학생 모두가 좋은 일이 아닐까? 라는 긍정적인 사고의 결과물이 바로 `Kuploy` 프로젝트이다.


## 학교의 컴퓨터들 그냥 원격으로 쓰면 안돼?


우리의 목적은 단순히 유휴 컴퓨터들 하나하나에 Teamviewer 를 깔아서 학생들에게 GUI 원격으로 사용할 수 있게 하거나, 아니면 그냥 ssh 를 열어서 사용할 수 있게끔 하는 것이 아니다. 만약 이런 것이 목적이였다면 프로젝트 이름은 Kuploy 가 아니라 Kumote(Kuploy + Remote) 가 되지 않았을까?


우리는 학교의 유휴 컴퓨팅 리소스 뿐만 아니라 개발 프로세스 자동화에도 관심이 많이 있었다.


학생들은 주로 AWS 를 이용해서 자신들의 프로젝트를 테스트하고 서비싱한다. 이렇게 서비싱하는 과정은 크게 아래의 3가지 절차를 밟는다.

1. AWS Free tier(신규 회원은 1년간 공짜니까!) ec2 생성
2. Inbound, Outbound 규칙 생성(일단 ssh 부터 뚫어놔야 나도 접근할 수 있으니까)
3. 프로젝트 build 파일들을 ec2 에 옮겨서 run 하거나, docker를 활용하여 run
4. 변경사항이 생긴다면 github 에 commit push 후, 3번과정을 다시 수행

> 이러한 과정은 복잡하지는 않지만 새로운 프로젝트를 할 떄마다 매번 해줘야하는 번거로움이 있다.


게다가 Free tier 는 성능이 썩 좋은 편은 아니다. 실제로 프로젝트를 진행하던 도중 ec2 의 메모리 부족으로 제대로 실행시킬 수 없는 경우도 있었다. 메모리 스왑을 통해 임시 방편을 마련해놨었지만 말 그대로 임시방편일 뿐이다.


이러다보니 매번 할 때마다 귀찮은 반복 작업이 필요하며, 성능에도 제한이 있어서 제대로 활용하기 힘든 AWS 를 대체하고 싶었다.


Kuploy 에서는 건국대학교 학생 한정으로, `Free tier 보다 높은 성능(물론, 상한선은 있다)을 제공하면서 동시에 배포 과정을 최적화`시킬 수 있다.


# Kuploy 프로젝트의 진행 과정


## Cluster 구축


우리는 우선 교수님으로부터 총 4대의 학교 컴퓨터를 대여 받았다.

1. 1대의 Master Node
2. 2대의 Worker Node
3. 1대의 Ansible Control Node

4대의 컴퓨터들은 이렇게 구성해서 클러스터를 구축하기로 결정하였고, 그 전에 4대의 컴퓨터들을 초기화해서 셋팅할 것이 많이 있었다.


### Master Node, Worker Node 공통 설정


### Ubuntu 22.04 Server LTS 설치


일단 [Ubuntu 22.04](https://releases.ubuntu.com/jammy/) 에서 설치할 OS 환경을 잘 골라서 Ubuntu 22.04 live server 의 iso 파일을 다운받자.


이제 부팅 디스크로 USB를 만들어야하는데, 집에 USB가 다 어딘가로 사라지고 없어서 SanDisk 사의 USB 2개를 구매하였다.


USB를 간단한 방법으로 설치 디스크로 만드는 툴은 크게 2개가 있다.

1. Rufus
2. Ventoy

이 중에 우리는 Ventoy 를 활용했다. 이유는 Rufus 보다 훨씬 쉽고 간단하다!
우선 [Ventoy](https://www.ventoy.net/en/download.html) 링크를 통해 본인의 OS에 맞는 Ventoy 툴을 다운받자.


![0](/upload/2025-01-14-[Kuploy_Project]_Kuploy_.md/0.png)_image.png_


이후에 Ventoy2Disk 를 실행하고 부팅 디스크로 만들 USB 를 선택한 후, Install 을 누르면 준비 끝이다.


이후에 iso 파일을 USB에 넣어주기만 하면 자동으로 부팅디스크가 된다.


이렇게 만든 부팅 디스크를 꽂고 설치하면 된다!
만약 위에서 만든 USB로 부팅되지 않는다면 BIOS 에서 부팅 순서를 바꿔보도록 하자.


### 스왑 메모리 끄기



```
shell
$ sudo swapoff -a

# 재부팅 후에 스왑 메모리가 자동으로 켜지는 것 방지
$ sudo vim /etc/fstab

# 스왑 파티션(아래 줄) 주석 처리
# /swap.img     none    swap    sw      0       0



```



쿠버네티스가 메모리를 할당할 때, 스왑메모리가 있으면 문제를 초래할 수 있다.
애초에 쿠버네티스가 리소스 관리를 하기 때문에 필요 하지도 않다. 스왑 메모리가 꺼졌는지 확인해보려면 `free` 커맨드를 사용해보면 된다.


> swapoff -a 는 일시적으로 스왑 메모리를 끄는 것이다. 만약 /etc/fstab 에서 스왑 메모리를 영구적으로 끄지 않는다면 컴퓨터를 재부팅했을 때 자동으로 스왑 메모리가 켜지게 된다. 이 경우에 쿠버네티스가 정상적으로 작동하지 않는다.


> 스왑 메모리가 켜진 상태로 kubectl get nodes 명령어를 입력해보면 Ready여야 하는 노드가 NotReady 상태로 변경되고, 해당 노드에서 실행중인 Pod 들은 Terminating 상태나 Pending 상태에 무한히 빠진다.


### 방화벽(ufw) 포트 allow


쿠버네티스 apiserver 가 6443 포트를 사용하기 때문에, 6443 포트를 뚫어주어야 api-server 와 통신할 수 있다.



```
shell
$ sudo ufw allow {port}


```



### kubectl, kubelet, kubeadm, docker 설치


최신 버전을 사용하면서 충돌 날 것을 우려하여 1.23.6-00 버전으로 명시하여 설치하였다.


설치 이전에 epel, net-tools 를 설치한다.


<details>
<summary>EPEL, net tools 는 뭘까?</summary>
<code>
<pre>


`EPEL` == Extra Packages for Enterprise Linux
말 그대로 엔터프라이즈 리눅스를 위한 추가 패키지이다. 기본 레포에 없는 오픈 소스들을 사용할 수 있다.


`net-tools` 는 리눅스의 네트워킹 관련 커맨드라인 도구 모음이다.


> A collection of programs that form the base set of the NET-3 networking distribution for the Linux operating system.  
> Includes: arp, hostname, ifconfig, netstat, rarp, route, plipconfig, slattach, mii-tool and iptunnel and ipmaddr.  
> A mirror of the sourcecode is available on https://github.com/ecki/net-tools


</pre></code>
</details>


<details>
<summary> 쿠버네티스 1.24 미만 버전 </summary>
<code>
<pre>



```
shell
# install Extra Packages for Enterpries Linux System, Docker
sudo apt-get install epel-release -y
sudo apt-get install net-tools -y
sudo apt-get update && sudo apt-get -y upgrade

# Install Kubernetes Package
# Add Google Cloud Public GPG Key to apt
sudo curl -s <https://packages.cloud.google.com/apt/doc/apt-key.gpg> | sudo apt-key add -

# Create new APT source list
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb <https://apt.kubernetes.io/> kubernetes-xenial main
EOF

# Update APT Package and upgrade all package
# flag -y means all yes
sudo apt-get update && sudo apt-get -y upgrade

# Install Kubernetes - kubectl, kubelet, kubeadm
sudo apt-get install -y kubelet=1.23.6-00 kubeadm=1.23.6-00 kubectl=1.23.6-00

# hold kubelet, kubeadm, kubectl version
sudo apt-mark hold kubelet kubeadm kubectl

echo "sleep 3 sec"
sleep 3

sudo apt-get update && sudo apt-get upgrade

sudo systemctl daemon-reload
sudo systemctl restart kubelet

echo "Installing Docker"
sudo curl -sSL <https://get.docker.com/> | sh

echo "sleep 1 sec"
sleep 1

echo "sleep 3 sec"
sleep 3


```



</pre></code>
</details>


쿠버네티스 버전을 1.24 이상 버전을 사용하면서, docker 에서 containerd 로 옮겨가게 되었다. 쿠버네티스와 containerd 의 설치 방법은 [k8s-docker-containerd]({{ site.url }}/kubernetes/2024/04/06/kubernetes_containerd.html) 에 적어두었다.


### containerd 설정



```
shell
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml


```



### systemd 를 cgroup driver 로 설정하기


`/etc/containerd/config.toml` 에 아래 항목 추가



```
shell
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true


```




```
shell
sudo systemctl restart containerd


```



containerd 재시작.


### IPv4 포워딩, bridge iptable 규칙 수정


[Container Runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd) 쿠버네티스 공식 문서를 참조해서 진행했다.



```
shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system


```



아래 커맨드로 br_netfilter 와 overlay 모듈이 load 되어 있는지 확인해보라고 한다.



```
shell
lsmod | grep br_netfilter
lsmod | grep overlay


```



이제 sysctl에 저 3가지 항목의 값이 1로 설정되어 있는지 확인해보자.



```
shell
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward


```



### cgroup


리눅스는 cgroup가 프로세스의 리소스를 제한하는 역할을 한다.


kubelet과 containerd 도 pod의 리소스를 제한하는데 cgroup driver 를 사용해야 하는데, 반드시 kubelet 과 containerd 는 같은 구성의, 같은 cgroup driver 를 사용해야 한다는 것이다.


cgroup driver 에는 `cgroupfs` 와 `systemd` 가 존재하는데 만약 init 프로세스가 systemd 인 경우에는 kubelet 과 containerd 의 cgroup driver 또한 `systemd` 를 사용하도록 권장하고 있다. 이유는 systemd 가 cgroup 관리자는 하나라고 인식하기 때문이다.


> 추가로 cgroup v2 를 사용할 경우에도 systemd 를 사용하라고 한다. cgroup v2 가 무엇인지 문서를 읽어보니 cgroup 의 업그레이드 버젼인듯 하다.


### kubelet 의 cgroup driver 를 systemd 로 수정하기


[kubelet-config-file](https://kubernetes.io/ko/docs/tasks/administer-cluster/kubelet-config-file/)


kubeadm 을 통해 init 을 하고 난 후에, kubelet 의 config 파일을 아래처럼 수정해주면 된다. 아직 kubeadm init 후에 적용하면 되기 때문에 지금 당장 config 파일을 만들 필요는 없다.


![1](/upload/2025-01-14-[Kuploy_Project]_Kuploy_.md/1.png)_image.png_


## Master Node 설정


### kubeadm init



```
shell
$ sudo kubeadm init --apiserver-advertise-address=[마스터 노드 IP] --pod-network-cidr=[CNI 네트워크 라우팅 대역]


```



마스터노드에서 kubeadm 을 init 하면서 `네트워크 라우팅 대역` 을 입력해야하는데, Calico 를 사용한다면 192.168.0.0/16 를 입력해 주면 된다.


cidr 에 관해서는 [Gateway,사설망,CIDR]({{ site.url }}/network/2024/03/22/cidr.html) 을 참조하자!


해당 커맨드를 입력하면 아래와 같은 결과값이 반환되는데 이를 어딘가에 적어두거나 기억해두자. 추후 Worker Node 설정에 필요하다.



```
shell
# 딱히 건들지 않았다면 api-server 의 포트가 6443 으로 설정되어 있을 것이라서 마스터 노드의 6443 포트를 입력한다.
sudo kubeadm join {마스터 노드 IP}:6443 --token {토큰값~}


```



이후에 config 에 권한을 부여해줘야 한다.



```
shell
sudo mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
systemctl restart kubelet
systemctl restart docker


```



### CNI(Container Network Interface)


CNI에 대한 자세한 내용은 따로 작성해둔 [Kubernetes-CNI]({{ site.url }}/kubernetes/2024/03/21/kubernetes_cni.html) 문서를 참조하자.


우리는 CNI 로 calico 를 선택했다. 사실 이렇게 소규모 프로젝트에서 어떤 CNI를 쓰든 큰 차이가 나지는 않을 것이다. 가장 대중적으로 사용하는 Calico, Flannel, Weave Net 정도의 차이점만 알아보았는데 대규모 트래픽 연산(성능) 측면에서는 L3를 활용하는, 즉 모든 Container 마다 ip를 부여해서 통신하는 Calico의 성능이 제일 좋았고, 간편성만 따졌을 때는 Flannel 이 제일 좋았다. WeaveNet은 Mesh 네트워크 구조라서 성능이 나머지 두 플러그인에 비해 조금 떨어진다.


대규모 트래픽 연산에 성능이 좋은 Calico와, 소규모 프로젝트에 어울리는 Flannel 중에서 선택을 고민했고 Calico 가 조금 더 대중적인 이유를 고려해서 Calico를 선택했다.


### Calico 설치



```
shell
# 2024-03-22 기준!
$ kubectl create -f <https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/tigera-operator.yaml>
$ kubectl create -f <https://raw.githubusercontent.com/projectcalico/calico/v3.27.3/manifests/custom-resources.yaml>


```



더 자세한 것은 [Calico 공식 홈페이지](https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart) 를 참조하자!


calico 가 예전의 프로젝트에서 분리되어 나온건지 예전과 달라진 적이 있으므로 공식 홈페이지에서 안내해주는대로 설치하는 것을 권장한다.



```
shell
$ watch kubectl get pods -n calico-system


```



이후에 위의 커맨드로 모든 calico pod 들의 Status 가 Running 으로 바뀌는지 확인한다. 대략 5~6분 정도 소요되는 것 같다.



```
shell
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
kubectl taint nodes --all node-role.kubernetes.io/master-


```



마지막으로 control plane 의 taint 를 제거함으로써 pod 스케쥴링을 할 수 있도록 바꿔준다.


### Helm 설치


Helm 은 쿠버네티스의 Package managing tool 이다. Linux 의 APT 나 YUM, 맥에서는 homebrew 와 비슷하다고 보면 된다.



```
shell
curl -fsSL -o get_helm.sh <https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3>
chmod +x get_helm.sh

helm version


```



## Worker Node 설정


Master Node 에서 kubeadm init 하면서 얻었던 join 커맨드를 입력해주자.



```
shell
# 딱히 건들지 않았다면 api-server 의 포트가 6443 으로 설정되어 있을 것이라서 마스터 노드의 6443 포트를 입력한다.
sudo kubeadm join {마스터 노드 IP}:6443 --token {토큰값~}


```



join 이 정상적으로 되었다면 master node 에서 `kubectl get nodes` 를 입력 했을 때, worker node 들의 상태가 NotReady 가 아닌 Ready 상태여야 한다.

