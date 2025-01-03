# 가상환경 세팅, 파일clone, export, 배포
  ### - Hyper-V 로 가상컴퓨터 생성 및 기본 세팅 

<br>

## 1. 가상컴퓨터 새로 만들기

- 1세대
- 메모리 할당 : 2048 / 체크해제   
- 네트워크 구성 : 외부스위치   
- 설치옵션 : 

  > 부팅가능 CD/DVD-ROM에서 운영체제 설치   
  > 
  > 이미지파일     > [ ubuntu-20.04.3-live-server-amd64.iso ] 선택
  >> C:\hjk2022\_iso설정파일\ubuntu-20.04.3-live-server-amd64.iso


## 2. VM 실행 후 설치과정

  - English
  - IPv4 Method  Manual  >>  ip설정 (동적X) :
    ```
      Subnet : 192.168.2.xx/32 (우선 32로 설정 > 변경 필요)
      Address : 192.168.2.xx
      Gateway : 192.168.2.1
      Name servers : 8.8.8.8,1.1.1.1
    ```
  
  - Proxy설정 : 빈칸으로 두면 됨
  - Mirror어드레스 : 기본 설정값 사용 ( http://mirror.kakao.com/ubuntu )
  - 프로필 설정
    ```
      name : comtrue
      server name : comtrue / hjkim
      pick a userName : comtrue / hjkim
      password : privacy!@34 
    ```

## 3. putty 접속 설정 
### 아래 내용은 Hyper-V 화면에서 작업 진행한다

- 접속 ip, port설정 후 open
- IP가 변경되지 않도록 설정하는 방법

    ```bash
    # 이미 사용중인 ip를 할당할 경우 충돌로 접속할 수 없으므로 사용하지 않는 ip 할당
    ifconfig eth0 192.168.2.xx netmask 255.255.255.0 up   
    
    # 권한이 없다고 뜸 :  sudo su 를 입력하여 root권한을 받아 명령 실행
    sudo su ifconfig eth0 192.168.2.xx netmask 255.255.255.0 up
    ```

+ root 권한 접속

    ```bash
    sudo su
    ```

    - sudo 사용법   
    1. sudo : 일반 사용자가 root 권한을 잠시 빌려 실행   
    2. su : 현재 사용자를 로그아웃하지 않은 상태에서 다른 사용자 계정으로 전환   
    3. su - : 다른 사용자 계정으로 완전히 전환하고, 전환된 사용자의 환경설정을 불러오는 명령어   
    ex) sudo su - root : 일반 사용자가 root권한을 빌려 반영구적으로 사용할 수 있게 함 (매번 sudo su 하기 번거로우니까)

<br>

+ ip설정

  ```bash
  # vi /etc/netplan/00-installer-config.yaml

  # This is the network config written by 'subiquity'
  network:
    ethernets:
      eth0:
        addresses:
        - 192.168.2.xx/24
        gateway4: 192.168.2.1
        nameservers:
          addresses:
          - 8.8.8.8
          - 1.1.1.1
    version: 2
  ```
  ```bash  
  # 00-installer-config.yaml :wq 로 저장 후
  netplan apply
  ```

+ ssh 설치

    ```bash
    # ssh 설치
    sudo apt-get install ssh

    # openssh-server 설치
    sudo apt-get install openssh-server

    # ssh 재시작
    sudo /etc/init.d/ssh restart  ||  systemctl restart sshd  ||  service ssh restart

    ```

+ port 변경
  ```bash
  # vi /etc/ssh/sshd_config
  Port 7022

  systemctl restart sshd
  ```

+ 관리자 비밀번호 변경

    ```bash
    # root 권한 접속 후 진행
    sudo su
    sudo passwd
    ```
    ```bash
    #vi /etc/ssh/sshd_config
    # 아래 주석 해제하고 yes 로 수정, 저장
    PermitRootLogin yes 

    systemctl restart sshd
    ```

### ※ linux 관련 추가 변경 사항 (필요 시 사용)

1.  mirror 서버 변경

    ```bash
    # vi /etc/apt/source.list

    # See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
    # newer versions of the distribution.
    deb http://kr.archive.ubuntu.com/ubuntu focal main restricted
    # deb-src http://kr.archive.ubuntu.com/ubuntu focal main restricted

    ## Major bug fix updates produced after the final release of the
    ## distribution.
    deb http://kr.archive.ubuntu.com/ubuntu focal-updates main restricted
    # deb-src http://kr.archive.ubuntu.com/ubuntu focal-updates main restricted

    ## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
    ## team. Also, please note that software in universe WILL NOT receive any
    ## review or updates from the Ubuntu security team.

    # ------------------------------
    # esc 누른 후 아래 코드 입력 > 일괄변경됨
    :%s/kr.archive.ubuntu.com/mirror.kakao.com

    ```

2. host name 변경
    ```bash
    # 적용 후 서버 새로 열어야 함.
    hostnamectl set-hostname {new-hostname}
    ```

### ※ 기본적인 vi 명령어

1. 명령모드

    ```bash
    i   : 현재 커서 위치에서 삽입
    a   : 현재 커서 다음 위치에 삽입
    o   : 현재 줄 다음 위치에 삽입
    x   : 커서가 위치한 곳의 글자 1개 삭제
          5x : 문자 5개 삭제
    dw  : 커서가 위치한 곳부터 단어 삭제
    dd  : 커거사 위치한 줄 잘라내기 (p 로 복구 가능)
          5dd : 5줄 잘라내기
    u   : 명령취소
    yy  : 복사
    p   : 붙여넣기
    G   : 파일 끝으로
    gg  : 파일 처음으로
    0(숫자) : 줄의 맨 앞으로
    숫자+   : 숫자만큼 아래줄로
    숫자-   : 숫자만큼 윗줄로
    ```

2. 마지막 행 모드 (esc :)

    ```bash
    w     : 파일 저장
            w [파일명] : 파일명으로 파일 저장
    q     : 종료
    q!    : 강제종료
    wq    : 저장 종료
    wq!   : 강제저장 종료
    e!    : 마지막 저장 이후 모든 편집 취소 
    숫자  : 해당 라인으로 커서 이동
    f [파일명]  : 파일명으로 파일이름 변경
    /문자열     : 현재 커서 위치에서 파일 앞쪽으로 문자열 탐색
    ?문자열     : 현재 커서 위치에서 파일 뒤쪽으로 문자열 탐색
    set nu      : 라인번호 출력
    set nonu    : 라인번호 출력 취소
    ```


## 4. JAVA_HOME 설정

- jdk 파일
  >   \\192.168.100.13\swdev_data\WEB_DEV\jdk\openJDK_linux\ openjdk-8u42-b03-linux-x64-14_jul_2022.tar.gz

- jdk 1.8 압축해제 

    ```bash
    # /usr/local 하위에 파일 이동 후 작업 진행했음.
    # openjdk-8u42-b03-linux-x64-14_jul_2022.tar.gz

    tar xvf openjdk-8u42-b03-linux-x64-14_jul_2022.tar.gz
    ```
<br>

- 환경변수 설정 (root JAVA_HOME 설정)

  1) 계정별 설정

      ```bash
      # vi ~/.bashrc

      export JAVA_HOME=/usr/local/java-se-8u42-ri
      export PATH=$JAVA_HOME/bin:$PATH
      ```

  2) 전체 설정

      ```bash
      # vi /etc/profile

      export JAVA_HOME=/usr/local/java-se-8u42-ri
      export PATH=$JAVA_HOME/bin:$PATH


      # :wq
      source /etc/profile
      ```


## 5. postgresql 설치 및 환경설정

1. 설치

    ```bash
    apt-get update

   # postgresql 설치
    apt-get install postgresql-12
    ```
    - apt-get update : 운영체제에서 사용 가능한 패키지들과 버전에 대한 정보를 업데이트하는 명령어 (설치리스트 업데이트)
    - apt-get upgrade : apt-get install 로 설치된 패키지들을 업그레이드하는 명령어

<br>

2. posgtresql 접속설정(1) (localhost) :: 안해도 됨

    ```bash
    # postgres 계정
    suo su - postgres   
    createuser -pwprompt --interactive postgres

    # postgresql DB 접속 (postgres라는 계정명으로 접속)
    psql -U postgres

    > create user root;   
    > alter role root superuser;
    > alter role root createrole;
    > alter role root createdb;

    # 이제 root 권한으로 postgresql접속 가능 (root계정으로 접속)
    psql root
    ```

<br>

3. postgresql 접속설정(2) (원격접속허용)

    ```bash
      # vi /etc/postgresql/12/main/postgresql.conf
        listen_address = '*'
    ```
    ```bash
    # vi /etc/postgresql/12/main/pg_hba.conf
        # Database administrative login by Unix domain socket
        # 로컬 superuser 접속 설정
        local   all             postgres                                peer

        # "local" is for Unix domain socket connections only
        local   all             all                                     trust
        
        # IPv4 local connections: 
        # 로컬 IP 허용
        #host   all             all             127.0.0.1/32            md5
        # 모든 IP 허용
        host    all             all             0.0.0.0/0               trust
        # 특정 IP 허용
        host    all             all             192.168.2.100/24        trust
        
        # IPv6 local connections: // 모든 IP 허용
        #host   all             all             ::1/128                 md5
        #
        
        # Allow replication connections from localhost, by a user with the
        # replication privilege.
        # 이중화 구성 시 복제를 위한 허용 지정
        #local   replication     postgres                                peer
        #host    replication     postgres        127.0.0.1/32            trust
        #host    replication     postgres        ::1/128                 trust


      # postgresql 재시작
      sudo systemctl restart postgresql
      ```
    - trust : 무조건 연결을 허용. 패스워드나 다른 인증 없이 임의의 데이터베이스 사용자로 로그인하여 누구나 데이터베이스 서버에 연결할 수 있음.
    - reject : 무조건 연결을 거부하는 것으로 특정 호스트 또는 사용자 등을 "필터링" 하여 차단 할 때 유용.
    - peer : 로컬 연결에서만 사용 할 수 있는 것. 운영 체제 사용자 이름을 데이타베이스 사용자 이름과 일치 시켜 인증.   
              예) 'postgres'  계정으로 local에서 superuser 권한 접속
    - md5 : 사용자 패스워드 방식 인증. 구버전 방식으로 암호화 알고리즘 취약성이 있어 사용을 지양함.
    - scram-sha-256 : 사용자 패스워드 방식 인증. sha256 패스워드 방식을 지원해야 함.

 
### ※ linux 에서 psql 로 DB 접속 시 -h 유무 차이점

  ```bash
    # tcp 가 아닌 unix domain소켓으로 붙음
    psql shpc

    # tcp 소켓으로 붙음
    psql -h 127.0.0.1 shpc

    ######
    # docker 에 DB가 올라가 있을 경우 -h 를 붙이지 않으면 tcp소켓이 아니기 때문에 DB에 붙을 수가 없음.
    docker exec -it {database} psql -h 127.0.0.1 ekyc  
  ```


## 6. tomcat

- tomcat 파일 복사
  > apache-tomcat-9.0.78.tar.gz

- tomcat 파일 압축 해제

    ```bash
    # /usr/local 하위에 파일 이동 후 작업 진행했음.
    # apache-tomcat-9.0.78.tar.gz

    tar xvf apache-tomcat-9.0.78.tar.gz

    # 리눅스 파일 권한 변경
    cd bin
    chmod 744 *.sh
    ```
    - 리눅스 파일 권한 관련 참고 : https://recipes4dev.tistory.com/175#google_vignette
