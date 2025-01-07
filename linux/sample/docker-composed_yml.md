## docker-compsed.yml  파일 내용
- 파일마다 변경이 필요한 부분
- 도커이미지 검색 : https://hub.docker.com/_/openjdk/tags?page=1&name=11.0-jdk
```yml
version: '3'
 
 services:
   pv-api-userport:
     image: openjdk:11.0-jdk-slim
     container_name: pv-api-userport
     environment:
       - LC_ALL=C.UTF-8
       - TZ=Asia/Seoul
       - LC_COLLATE=C
       - server.port=port
       - spring.datasource.hikari.jdbc-url=jdbc:postgresql://127.0.0.1/privacycenter_private
     restart: always
     volumes:
         - $PWD/:/usr/local/privacycenter/
     entrypoint:
         - java
         - -jar
         - /usr/local/privacycenter/api.war
     network_mode: "host"
```

## script
스크립트 작업
- 사용자를 추가할 경우 DB생성, 비밀번호생성, docker-compose 를 진행할 스크립트 작성
- 스크립트 실행 시 loginid, email, 사용자별server_port 를 입력하면 자동으로 입력되도록 작업
```bash
sh  pv_cloud_insertuser.sh 'loginid' 'email' 'server_port'
```
```bash
#!/bin/bash

LOGINID=$1
EMAIL=$2
PORT=$3

DB_HOST=192.168.2.101
DB_PORT=5432

API_ROOT=/usr/local/privacycenter_api/
API_ORIGIN_FILE=/usr/local/privacycenter_api/pv_origin.yml

PASSWD=$(openssl rand -base64 6)
 
# 랜덤한 사용자 비밀번호 암호화
PASSWDENC=$(echo -n $PASSWD | shasum -a 256 | cut -c 1-64)

# 사용자정보 insert
PGPASSWORD='privacy!@34' psql -h ${DB_HOST} -p ${DB_PORT} -U 'root' -d 'privacycenter_login' -c "insert into admin_info (loginid, passwd, email) values ('$LOGINID', '$PASSWDENC', '$EMAIL')";
## ------------------------------------------------
# admin_info 테이블이 정확하게 확정되지 않음. 추가 수정이 필요함
# 사용자 입력 시 server_port, server_host, db_port, db_host 를 추가로 입력하게 할 수도 있음
## ------------------------------------------------

# 사용자 개별 DB명 조회
DATABASE_NAME=$(PGPASSWORD='privacy!@34' psql -t -h ${DB_HOST} -p ${DB_PORT} -U 'root' -d 'privacycenter_login' -c "select concat('privacycenter_',last_value) from admin_info_id_seq" )
DATABASE_NAME=$(echo ${DATABASE_NAME})

#사용자 개별 DB생성
PGPASSWORD='privacy!@34' psql -h ${DB_HOST} -p ${DB_PORT}  -U 'root' -d 'privacycenter_login' -c "CREATE DATABASE $DATABASE_NAME WITH TEMPLATE=template0 OWNER=postgres";
PGPASSWORD='privacy!@34' psql -h ${DB_HOST} -p ${DB_PORT}  -U 'root' -d 'privacycenter_login' -c "ALTER DATABASE $DATABASE_NAME SET bytea_output TO 'escape'";
PGPASSWORD='privacy!@34' psql -h ${DB_HOST} -p ${DB_PORT}  -U 'postgres' -d $DATABASE_NAME  -a -f /usr/local/sql/pv/pv-cloud-api.sql

#샘플파일 복사 후 개별파일로 수정
FILE_NAME=pv_${PORT}.yml
cp $API_ORIGIN_FILE ${API_ROOT}${FILE_NAME}
sed -i "s/server.port=port/server.port=${PORT}/g" ${FILE_NAME}
sed -i "s/privacycenter_private/${DATABASE_NAME}/g"  ${FILE_NAME}
## api 서버별 명칭 수정을 위함
sed -i "s/pv-api-userport/pv-api${SERVER_PORT}/g"  ${FILE_NAME}


docker_compose -f ${FILE_NAME} up -d

# -------------------------------------------
echo "$API_ORIGIN_FILE ${API_ROOT}pv_${PORT}.yml"
echo
echo 'adminId  ' $USERID
echo 'adminPw  ' $PASSWD
echo
echo 'admin private Database Name    ' $DATABASE_NAME
# -------------------------------------------
```
