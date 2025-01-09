
# java.library.path 확인
```bash
# 터미널에서 java 관련 환경변수 확인
java -XshowSettings:properties -version
```

# jeus8 설치
- jdk1.7 이상
- 업로드 파일 실행

#### 1. 환경변수 적용(설치 후 JEUS_PATH가 자동으로 갱신되므로 적용 필요)
```bash
    # vi ~/.bash_profile ** 맨 아래 추가
    export JAVA_HOME=/usr/jdk/jdk-8u191
    export PATH=$JAVA_HOME/bin:$PATH
    export JEUS_HOME=/home/jeus
```
- source ~/.bash_profile

#### 2. [jeus8 설치](https://velog.io/@chrisantus/Linux-JEUS-8-%EC%84%A4%EC%B9%98)

#### 3. license 적용
-  [license 발급](https://technet.tmaxsoft.com/ko/front/main/main.do)
	- 발급유형 Demo
	- Host Name :: 서버 hostname 확인 후 적을 것 (localhost.localadmin)

#### 4. [jeus 실행](https://blog.naver.com/tawoo0/221559022526)
```bash
#  DAS(Domain Admin Server) 시작
startDomainAdminServer -domain jeus_domain -u admin -p privacy

# nodeManager 시작
nohup startNodeManager > /home/jeus/nodemanager/logs/nm.log &

# MS(Managed Server) 시작/중지
startManagedServer -dasurl localhost:9736 -domain jeus_domain -server server1 -u admin -p privacy
```

#### 5. 방화벽설정
- 추가
	```bash
	# 방법1 (범위허용)
    firewall-cmd --add-port=9736-9800/tcp
	firewall-cmd --runtime-to-permanent
    firewall-cmd --reload

	# 방법2 (단일허용)
	firewall-cmd --permanent --zone=public --add-port=80/tcp

	# 방화벽 해제(사용하지 말 것)
	systemctl stop firewalld
	```

- 추가확인
	```
    firewall-cmd --list-port
	```

### 6. 관리페이지 접속
http://[서버 IP]:9736/webadmin
```
# 로그인계정 : <id> / <password>    // id(default): administrator
```

** jeus 서버 추가 및 배포방법   https://hanjonghyuk.tistory.com/13


## ERROR.
Server 시작 시 STANDBY 상태로 서버가 대기상태로 되는 상황
>> 방화벽문제로 확인. 해결


#### 아래 에러가 발생하며 war 파일이 배포되지 않음.
```
[2023.10.11 17:54:43][2] [server1-34] [Deploy-0232] Processing annotations of the class [META-INF/versions/9/module-info.class] failed but the deployment process will continue.
<<__Exception__>>
java.lang.IllegalArgumentException
	at jeus.thirdparty.asm504.ClassReader.<init>(ClassReader.java:170)
	at jeus.thirdparty.asm504.ClassReader.<init>(ClassReader.java:153)
	at jeus.thirdparty.asm504.ClassReader.<init>(ClassReader.java:424)
	at jeus.deploy.util.CDIClassAnnotationChecker.hasAnnotation(CDIClassAnnotationChecker.java:127)
	at jeus.cdi.util.BeanArchiveSniffer.searchUserDefinedAnnotations(BeanArchiveSniffer.java:93)
	at jeus.cdi.util.BeanArchiveSniffer.searchUserDefinedAnnotations(BeanArchiveSniffer.java:350)
	at jeus.cdi.util.CDIScanner.scanWar(CDIScanner.java:75)
	at jeus.servlet.engine.Context.searchAnnotationsForCDIArchive(Context.java:1747)
	at jeus.servlet.engine.Context.init0(Context.java:776)
	at jeus.servlet.engine.Context.init(Context.java:523)
	at jeus.servlet.engine.VirtualHost.deployContext(VirtualHost.java:110)
	at jeus.servlet.common.WebContainerManager.createAndAddContext(WebContainerManager.java:773)
	at jeus.servlet.common.WebContainerManager.deployContext(WebContainerManager.java:748)
	at jeus.servlet.deployment.WebModuleDeployer.distribute1(WebModuleDeployer.java:164)
	at jeus.deploy.deployer.AbstractDeployer.distribute(AbstractDeployer.java:239)
	at jeus.deploy.deployer.DeploymentAdministrator.distribute(DeploymentAdministrator.java:245)
	at jeus.deploy.deployer.DeploymentAdministrator.distribute(DeploymentAdministrator.java:178)
	at jeus.server.service.internal.ServerDeploymentService.distribute(ServerDeploymentService.java:166)
	at jeus.server.service.internal.ServerDeploymentService.distribute(ServerDeploymentService.java:203)
```
