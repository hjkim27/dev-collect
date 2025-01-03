---
title : centOS, redhat settings
date : 2023
type : linux
---
# vm 구축
## 1. rhel8.6 
- rhel ios 설정 방법 : https://docs.google.com/document/d/1HAS8_JwR7sqWeszg7SF0XR7ouoceIuSLBTRiiZylgoQ/edit?usp=sharing

## 2. yum 설정
### 1. os 업데이트 방지
```bash
# vi /etc/yum.conf
[main]
gpgcheck=1
installonly_limit=3
clean_requirements_on_remove=True
best=True
skip_if_unavailable=False

#### 아래 내용 추가
exclude=centos-release*,kernel*
```
- [[CentOS] Yum Update 시 버전 고정시키기](https://m.blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=jsmb&logNo=221057837883&proxyReferer=)

### 2. yum update
> yum update
- redhat minimal 설치후 yum update 하면 아래와 같은 에러 발생.
- RedHat에서 제공되는 RHEL(RedHat Enterprise Linux)를 사용하기 위해서는 subscription 등록이 필요함.
- RHEL을 다운받은 후 subscription을 등록 안 하게 되면 repository 및 기타 RedHat 기능을 사용할 수 없음
```
Updating Subscription Management repositories.
Unable to read consumer identity
This system is not registered with an entitlement server. You can use subscription-manager to register.
Error: There are no enabled repositories in "/etc/yum.repos.d", "/etc/yum/repos.d", "/etc/distro.repos.d".
```

- 계정정보 사용 가능
    - username, password : redhat 사이트 계정 정보
    - hjkim2.comtrue / privacy!@34
    > subscription-manager register --username `<username>` --password `<password>` --auto-attach

- 현재는 계정정보가 만료되지 않아 subscription-manager 설정 사용 (다음달 만료 예정)
- 계정정보 만료 시 관련 내용은 [rhel ios 설정방법](https://docs.google.com/document/d/1HAS8_JwR7sqWeszg7SF0XR7ouoceIuSLBTRiiZylgoQ/edit) 구글문서에 정리하였음

---

yum list installed ssh
>>  Updating Subscription Management repositories.
    This system is registered to Red Hat Subscription Management, but is not receiving updates. You can use subscription-manager to assign subscriptions.
    Error: no matching Packages to list

1. rhel 구독 활성화 및 yum update 진행
```bash
    # username과 password는 redhat 사이트의 계정 정보.
    # hjkim2.comtrue   /   privacy!@34
    subscription-manager register --username <username> --password <password> --auto-attach    
    yum update
```

2. 필요항목 다운로드
```bash
    # ssh
        yum -y install openssh-server openssh-clients openssh-askpass
    # lrzsz
        yum install lrzsz
    # tar
        yum install tar
    # net-tools (provides: netstat, ifconfig)
        yum install net-tools
```

## 방화벽 설정
#### centos/redhat 의 경우 방화벽 설정 필요
```bash
firewall-cmd --zone=public --permanent --add-port=7070/tcp
firewall-cmd --reload
```
- 참고URL : https://velog.io/@duck-ach/Linux-RedHat-8-CentOS-%EB%B0%A9%ED%99%94%EB%B2%BD-%ED%8F%AC%ED%8A%B8-%EB%B2%88%ED%98%B8-%EC%97%B4%EA%B8%B0
