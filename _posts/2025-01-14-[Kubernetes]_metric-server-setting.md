---
layout: post
current: post
cover: "assets/images/cover/kubernetes.png"
navigation: True
title: "[Kubernetes] metric-server-setting"
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

# [Kubernetes] metric-server-setting


`High A metric server`



```
text
kubectl apply -f <https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml>


```



`kubectl edit deployments.apps -n kube-system metrics-server`



```
text
- --kubelet-insecure-tls=true
- --kubelet-preferred-address-types=InternalIP

....

      dnsPolicy: ClusterFirst
      hostNetwork: true # << 추가
      nodeSelector:
        kubernetes.io/os: linux


```



`sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml`



```
text
--enable-aggregator-routing=true


```


