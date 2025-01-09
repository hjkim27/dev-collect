# nginx
- 가볍고 높은 성능을 가진 웹서버(web Server)
- HTTP Server 로 활용, 정적 파일들을 처리하기 위해 사용됨
- Reverse Proxy Server 로 활용  >> 80 으로 들어오는 내용을 3000, 4000, 5000 등 다른 포트로 분산시킬 수 있음
- 비동기 이벤트 구조를 기반으로 동작


## nginx / apache 차이
서버의 동자 방식 차이

#### nginx :: Event-Driven(비동기처리방식)
- 서버로 들어오는 여러 커넥션을 Event-Handler 를 통해 비동기방식으로 처리하게 함        
    \> 적은 메모리로 서버 운영 가능
- 싱글 스레드, 프로세스로 작동
- socket read/writer, I/O 등 CPU가 관여하지 않아도 되는 작업을 진행할 때 기다리지 않고 다른 작업 수행       
    진행중인 I/O 등 작업이 종료되면 이벤트 발생, 다음 작업 처리 시작

#### apache :: Thread/Process
- 클라이언트 접속시마다 thread 또는 process 생성 처리       
    \> 많은 수의 요청 발생 시 속도가 느려짐
- 다양한 모듈 제공, 확장성이 좋음

# nginx 설치

## package를 통한 nginx 설치
```
sudo apt-get install nginx
```
## version확인
```
nginx -v
```

# nginx 사용 명령어
```bash
# 시작
sudo service nginx start
sudo systemctl start nginx

# 재시작
sudo service nginx restart
sudo systemctl restart nginx

# 중지
sudo service nginx stop
sudo systemctl stop nginx

# 상태
sudo service nginx status
sudo systemctl status nginx

# 설정 reload
sudo service nginx reload
sudo systemctl reload nginx

# configuration file syntax check
sudo nginx -t
```
