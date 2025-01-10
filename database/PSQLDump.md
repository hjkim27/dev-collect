# pg_dump [postgresql]
## 명령어실행방법
### 1. postgres 으로 계정 변경 후 명령어만 입력
```bash
# postgres 접속
root$ su postgres

# postgres계정으로 명령어 실행
postgres$ createdb [DB명] --lc-collate='C' -T template0
postgres$ psql [DB명] < ./dump.sql
```
### 2. su postgres 를 통해 권한 부여 후 명령어 입력
```bash
# 각 명령어마다 postgres 권한 부여 후 명령어 확인
root$ su postgres -c "createDB [DB명] --lc-collate='C' -T template0"
root$ su postgres -c "psql [DB명] < ./dump.sql"
```

## 명령어
### postgres 계정 변경
```bash
su postgres
```
### DB 생성 및 삭제
```bash
# 생성
## --lc-collate : 언어set 설정
## tempate0     : 한글 정렬을 위해 template0 사용 필요
createdb [DB명] --lc-collate='C' -T template0

# 삭제
dropdb [DB명]
```

### 계정생성 및 권한 부여
```bash
# 계정 생성
psql -c 'create user root'

# 권한 부여
psql -c 'alter role root superuser;'
psql -c 'alter role root createrole;'
psql -c 'alter role root createdb;'
```

## 원격으로 psql 붙기
psql -h [ ip ] -p [ port ] -U [사용자계정] -d [DB명]
```bash
# example
psql -h pg-14jhe.vpc-cdb-kr.gov-ntruss.com -p 5432 -U comtrue -d privacycenter_login
psql -h pg-14jhe.vpc-cdb-kr.gov-ntruss.com -p 5432 -U root -d privacycenter_master
```

## DB backup(dump)
pg_dump -U [계정명] -t [테이블명] -a -O --inserts -E UTF8 [데이터베이스명] > dump.sql
```bash
# example
pg_dump -U root -O -E UTF8 [DB명] > ./dump.sql
```
### dump option
| option   | mean               | description                           | 
|----------|--------------------|---------------------------------------|
|-U        |--username=NAME     | connect as specified database user    |
|-t        |--table=TABLE       | dump the named table(s) only          |
|-a        |--data-only         | dump only the data, not the schema    |
|-O        |--no-owner          | skip restoration of object ownership in plain-text format |
|--inserts |                    | dump data as INSERT commands, rather than COPY  |
|-E        |--encoding=ENCODING | dump the data in encoding ENCODING |

## DB rollback

psql [DB명] < ./dump.sql


# DB에서 값을 조회해 csv 파일로 저장하기
```bash
#!/bin/bash

if [ $# -ne 1 ] 
then
    echo "No argument supplied"
    echo "./save_face_image.sh YYYY-MM-DD"
    exit
fi
STARTDATE=$1

DB_HOST=127.0.0.1
DB_PORT=5432
DB_PASSWD='privacy!@34'

FILE_NAME=/tmp/save_image/faceData.csv

# 아래와 같이 쿼리문을 바로 조회해도 됨.
## ex)
psql -d '{DB명}' -c "\COPY ({select 쿼리}) TO '{파일이름}' {확장자};";

## 실제 실행 코드
psql -d 'face_aidee' -c "\COPY (select concat(name,'_', id), data from ( select  t_img.id, t_upt.name, t_img.data from (select id, user_id, image_id, face_id from checkin_event where created_at >= '$STARTDATE') t_check left join ( select user_id, replace(data, ' ', '_') as name from user_property where user_property_type_id=1 ) t_upt on t_check.user_id = t_upt.user_id left join ( select id, data from image ) t_img on t_check.image_id = t_img.id union select  t_img.id, t_upt.name, t_img.data from (select id, user_id, image_id from face where created_at >= '$STARTDATE') t_face left join ( select user_id, replace(data, ' ', '_') as name from user_property where user_property_type_id=1 ) t_upt on t_face.user_id = t_upt.user_id left join ( select id, data from image) t_img on t_face.image_id = t_img.id	 ) b ) TO '$FILE_NAME' csv;";

# rm /tmp/save_image/image/*
# java -jar /tmp/save_image/saveFace-1.0-jar-with-dependencies.jar
```
