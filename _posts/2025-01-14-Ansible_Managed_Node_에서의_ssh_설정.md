---
layout: post
current: post
cover: "assets/images/cover/ansible.png"
navigation: True
title: "Ansible Managed Node 에서의 ssh 설정"
date: 2025-01-14 02:14:00
tags:
    - [Ansible, ]
class: post-template
subclass: 'post'
author: 
  - "hoeeeeeh"
categories:
    - [IaC, ]
---

# [Ansible] Managed Node 에서의 ssh 설정


## SSH


[Ansible 공식 문서](https://docs.ansible.com/ansible/latest/index.html)


[Building Ansible Inventories](https://docs.ansible.com/ansible/latest/inventory_guide/index.html)

1. 우선 테스트로 진행할 Worker Node 1 에, ansible 이 ssh로 접근해서 사용할 사용자 계정을 하나 만들어주었다.


```
text
$ sudo adduser ansible
$ sudo passwd ansible


```


1. 이후 ansible 계정이 sudo 권한을 비밀번호 없이 사용할 수 있도록 설정했다.


```
text
$ sudo visudo

...
# User privilege specification
root    ALL=(ALL:ALL) ALL
ansible ALL=(ALL:ALL) NOPASSWD:ALL
...


```


1. Control Node 에 sshpass 를 설치한다.


```
text
$ sudo apt-get install sshpass


```


1. Control Node 에서 공개키/개인키 를 생성한다.
공개키/ 개인키는 `/home/{user}/.ssh` 에 성생된다.


```
text
$ sudo ssh-keygen


```


1. 공개키/개인키를 Managed Node 에 보내기


```
text
$ ssh-copy-id -p {port} ansible@{ip_address}


```



ssh-copy-id -p 6001 [ansible@192.168.0.11](mailto:ansible@192.168.0.11)
Managed Node 에서 ssh 포트를 바꾸었기 때문에 -p 옵션을 사용했다.

