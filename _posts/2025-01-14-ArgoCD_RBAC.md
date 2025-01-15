---
layout: post
current: post
cover: "assets/images/cover/argocd.png"
navigation: True
title: "ArgoCD RBAC"
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

# [ArgoCD] RBAC


[Argocd 공식문서](https://argo-cd.readthedocs.io/en/stable/getting_started/)


## Argocd RBAC


졸업프로젝트로 쿠버네티스와 CD tool 로 Argocd 를 선택했다.


Argocd 설치를 끝마치고, admin 계정의 초기 비밀번호까지 바꿔준 후에 간단하게 application 을 만들어보려고 했는데, Permission Denied 가 떴다.


아니 어째서?


알고보니 Argocd 도 쿠버네티스의 Service Account 처럼 RBAC 로 role 을 부여해야만 했던 것이었다.


그래서 우선 Argocd 의 RBAC config 파일을 불러왔다.



```
text
kubectl get configmap argocd-rbac-cm -n argocd -o yaml > argocd-rbac.yml


```



이후에 argocd-rbac.yml에 아래 권한들을 추가해 주었다.



```
yaml
apiVersion: v1
data:
  policy.csv: |
    # Built-in policy which defines two roles: role:readonly and role:admin,
    # and additionally assigns the admin user to the role:admin role.
    # There are two policy formats:
    # 1. Applications, logs, and exec (which belong to a project):
    # p, <user/group>, <resource>, <action>, <project>/<object>
    # 2. All other resources:
    # p, <user/group>, <resource>, <action>, <object>

    p, role:readonly, applications, get, */*, allow
    p, role:readonly, certificates, get, *, allow
    p, role:readonly, clusters, get, *, allow
    p, role:readonly, repositories, get, *, allow
    p, role:readonly, projects, get, *, allow
    p, role:readonly, accounts, get, *, allow
    p, role:readonly, gpgkeys, get, *, allow
    p, role:readonly, logs, get, */*, allow

    p, role:admin, applications, create, */*, allow
    p, role:admin, applications, update, */*, allow
    p, role:admin, applications, delete, */*, allow
    p, role:admin, applications, sync, */*, allow
    p, role:admin, applications, override, */*, allow
    p, role:admin, applications, action/*, */*, allow
    p, role:admin, applicationsets, get, */*, allow
    p, role:admin, applicationsets, create, */*, allow
    p, role:admin, applicationsets, update, */*, allow
    p, role:admin, applicationsets, delete, */*, allow
    p, role:admin, certificates, create, *, allow
    p, role:admin, certificates, update, *, allow
    p, role:admin, certificates, delete, *, allow
    p, role:admin, clusters, create, *, allow
    p, role:admin, clusters, update, *, allow
    p, role:admin, clusters, delete, *, allow
    p, role:admin, repositories, create, *, allow
    p, role:admin, repositories, update, *, allow
    p, role:admin, repositories, delete, *, allow
    p, role:admin, projects, create, *, allow
    p, role:admin, projects, update, *, allow
    p, role:admin, projects, delete, *, allow
    p, role:admin, accounts, update, *, allow
    p, role:admin, gpgkeys, create, *, allow
    p, role:admin, gpgkeys, delete, *, allow
    p, role:admin, exec, create, */*, allow

    g, admin, role:admin
    g, admin-k8s, role:admin


```



admin 과 read-only 권한을 정의하고, admin 과 admin-k8s 에 admin 권한을 부여한다


이후 적용해주면 된다.



```
text
kubectl apply -f argocd-rbac.yml


```


