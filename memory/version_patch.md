# jdk
ex) 1.7 > 1.8 업그레이드
```
tar xvf jdk-8u191-ojdkbuild-linux-x64.tar.gz
mv jdk-8u191-ojdkbuild-linux-x64 /usr/local/jdk-8u191
update-alternatives --install /usr/bin/java   java  /usr/local/jdk-8u191/bin/java  1
update-alternatives --install /usr/bin/javac  javac /usr/local/jdk-8u191/bin/javac 1
update-alternatives --install /usr/bin/jps    jps   /usr/local/jdk-8u191/bin/jps   1
```
### 환경변수 설정 및 적용
```bash
# vi /etc/profile
JAVA_HOME=/usr/local/jdk-8u191
#JAVA_HOME=$SHPC/jdk1.7
# :wq

source /etc/profile
```

# tomcat 
#### 기존 tomcat은 shutdown.sh 실행 후 작업 진행
```
mv /usr/local/shpc/tomcat /usr/local/shpc/tomcat7
tar -zxvf tomcat.tar.gz
mv tomcat /usr/local/shpc/
cd /usr/local/shpc/tomcat/bin
chmod 744 *.sh
```

### 3) jdk, tomcat 버전 변경 후 접속중이던 ssh 접속 해제 후 재접속
** tomcat startup.sh 실행 시 jdk가 정상적으로 변경되었을 경우 아래 JRE_HOME 으로 확인
```
Using JRE_HOME:        /usr/local/jdk-8u191
```

```
tar xvf jdk-8u191-ojdkbuild-linux-x64.tar.gz
mv jdk-8u191-ojdkbuild-linux-x64 ./lib

alternatives --install ~/bin/java java ~/lib/jdk-8u191/bin/java 1
alternatives --install ~/bin/javac javac ~/lib/jdk-8u191/bin/javac 1
alternatives --install ~/bin/jar jar ~/lib/jdk-8u191/bin/jar 1
alternatives --set java ~/lib/jdk-8u191/bin/java
alternatives --set javac ~/lib/jdk-8u191/bin/javac
alternatives --set jar ~/lib/jdk-8u191/bin/jar

alternatives --config java
```
