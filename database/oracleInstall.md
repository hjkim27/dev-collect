# Oracle database

1. 오라클 설치
     - 오라클 홈페이지에서 리눅스용 oracle 11g xe 버전 다운로드
     - 파일 옮기고 아래 순서대로 진행

1) 리눅스용 rpm 파일을 압축해서 배포하므로 압축해제
```
unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip
```

2) rpm파일을 ubuntu 에서 사용하는 deb 파일로 변환
```
sudo apt install alien 
cd Disk1
sudo alien --scripts oracle-xe_11.2.0-1.0.x86_64.rpm
```

3) deb 파일 설치
```
sudo dpkg -i oracle-xe_11.2.0-2_amd64.deb
```
4) 설정 스크립트 실행
```
sudo ln -s /usr/bin/awk /bin/awk
sudo mkdir /var/lock/subsys
```

5) 설정 스크립트 초기설정 진행
```
sudo /etc/init.d/oracle-xe configure
```

-----
HTTP 포트 [8080]    
데이터베이스 리스너 포트 [1521]    
암호지정: oracle    
암호확인: oracle    
부팅시 Oracle 시작? y    

-----

** oracleDB시작 종료 명령어
```
sudo /etc/init.d/oracle-xe start 
sudo /etc/init.d/oracle-xe stop
```

** redhat 패키지는 설치 시 /sbin/chkconfig 를 사용하지만 ubuntu 에는 없음

6) 동일환경을 만들어주기 위해 해당 파일 생성 후 아래내용 추가
```bash
# sudo vi /sbin/chkconfig

-----
#!/bin/bash


Oracle 11gR2 XE installer chkconfig hack for Ubuntu

file=/etc/init.d/oracle-xe

if [[ ! `tail -n1 $file | grep INIT` ]]; then

echo >> $file

echo '### BEGIN INIT INFO' >> $file

echo '# Provides: OracleXE' >> $file

echo '# Required-Start: $remote_fs $syslog' >> $file

echo '# Required-Stop: $remote_fs $syslog' >> $file

echo '# Default-Start: 2 3 4 5' >> $file

echo '# Default-Stop: 0 1 6' >> $file

echo '# Short-Description: Oracle 11g Express Edition' >> $file

echo '### END INIT INFO' >> $file

fi

update-rc.d oracle-xe defaults 80 01

#EOF
-----

# sudo chmod 755 /sbin/chkconfig
```

7) 환경변수 추가
    - oracle관련 환경변수 추가 필요
```bash
# vi ~/.bashrc

-----

export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe

export ORACLE_SID=XE

#export NLS_LANG=$ORACLE_HOME/bin/nls_lang.sh

export NLS_LANG=KOREAN_KOREA.KO16MSWIN949

export ORACLE_BASE=/u01/app/oracle

export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH

export PATH=$ORACLE_HOME/bin:$PATH

-----

# source ~/.bashrc
```


8) 오라클 접속
```
sqlplus system
oracle
```


** 오라클 사용자 계정 생성
```
create user root identified by 1234;
grant connect, resource, dba to root;
commit;
```

