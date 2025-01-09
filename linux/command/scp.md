## 1. 단일파일 전송
```bash
#scp [옵션] [파일명] [원격지계정]@[원격지IP]:[파일경로]
scp -p 7022 test.sql root@192.168.2.101:/usr/local/sql
```
- 현재경로의 test.sql 파일을 원격지(192.168.2.101:7022) 서버의 /usr/local/sql 경로로 전송한다.

--------------------


## 2. 복수파일 전송
```bash
# scp [옵션] "[파일명(1)] [파일명(2)] [파일명(3)]" [원격지계정]@[원격지IP]:[파일경로]
scp -p 7022 "test.sql test2.sql" root@192.168.2.101:/usr/local/sql
```
- 현재경로의 test.sql, test2.sql 파일을 원격지(192.168.2.101:7022) 서버의 /usr/local/sql 경로로 전송한다.

--------------------


## 3. 디렉토리 전송
```bash
# scp [옵션] [디렉토리명] [원격지계정]@[원격지IP]:[파일경로]
scp -p 7022 /usr/local/sql/pv root@192.168.2.101:/usr/local/sql
```
- /usr/local/sql 하위의 pv 디렉토리를 원격지(192.168.2.101:7022) 서버의 /usr/local/sql 경로로 전송한다.

