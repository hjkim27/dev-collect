# war파일 service 배포방법
## war 서비스 등록 자동실행
- root 계정 혹은 su/sudo 권한 필요

1. service 등록 확인
```bash
ps -aufx | grep java 
```

2. service 설정파일 작성 >> service name 을 TEST 라고 가정
```bash
# vi /etc/systemd/system/${service name}.service
# -----------------------------------------------------

[Unit]
# 서비스에 대한 설명
Description=api_calculator spring boot app          
# 서비스가 언제 실행될지에 대해 정할 수 있음. 서비스 실행 전 먼저 설정되거나 실행되어야 하는것이 있을 경우 작성
After=postgresql.service                            ## postgresql.service 가 실행된 후 실행

[Service]
# jar 파일 실행 부분
# /bin/bash -c "exec -jar {jar 파일 실행 명령어 작성}"
## ~/api_calculator.war 파일을 logback.xml, application.properties 를 각 저 파일을 적용해서 실행
ExecStart=/bin/bash -c "exec java  -jar /usr/local/api_calculator/api_calculator.war  \
    -Dlogback.configurationFile=/usr/local/api_calculator/logback.xml  \
    --spring.config.additional-location=file:///usr/local/api_calculator/application.properties"
    
# 서비스가 언제 다시 시작할지에 대한 것을 설정할 수 있는 부분
## (on-failure : 실패하면 다시 시작/ always : 서비스가 정상종료되었어도 다시 시작 / no:재시작안함(default))
Restart=always                  

# 서비스 재시작 전 delay 시간
## 3초 delay (default : 100ms) 
RestartSec=3

# 서비스를 실행하기 위한 권한
#User=root
#Group=root     


[Install]
# service run Level 설정 부분
## multi-user.target : 다중 사용자 설정
WantedBy=multi-user.target      
```
3. 작성 파일 service 등록
```bash
demon-reload ${service name}.service
```

4. 서비스 재시작
```bash
systemctl restart ${service name}.service
```

## 서비스 상태 확인 (아래 중 하나 실행)
1) systemctl -t service list-unit-files    
    - 현재 구동되는 서비스 목록 확인
2) systemctl status ${service name}.service
    - service 가 disabled 되어있을 경우 서버 재시작 시 재시작되지 않음.

### enable 처리
```bash
systemctl enable ${service name}.service
```
