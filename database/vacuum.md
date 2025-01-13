# [ISSUE]
## 상태 확인
psql  쿼리 업데이트 진행 중 /var/lib/postgresql 폴더의 크기가 점점 늘어나는 현상 발견
- 현재 206G > 287G 로 증가한 것으로 확인되며, 계속 증가할 것으로 보임
- 실행한 쿼리 : update_V2.0.9.0.sql    
    - alter table, create table, create trigger 등의 쿼리 다수 존재
- 실행중인 쿼리 : `alter table pcscan_current_detail_result add column document_classification integer not null default 0`
- psql 테이블 부풀림 현상으로 예상됨        
\> 다수의 테이블을 대상으로 update, delete, alter, trigger 를 진행하고 있어 테이블 부풀림 현상이 발생한 것으로 보임

# [Vacuum] 뜻 및 필요성
### 참고URL
- [PostgreSQL 테이블 부풀림 현상 대처 방안](https://postgresql.kr/blog/postgresql_table_bloating.html)
#### 1. 테이블 부풀림 현상
PostgreSQL에서 해당 테이블 대상으로 UPDATE, DELETE 명령을 이용해서 자료 조작이 일어난다면, 그 테이블 크기는 실 자료 크기보다 커질 수 밖에 없음.    
사용자의 자료 조작 뿐만 아니라, 트리거나, 함수를 통해서 일어나는 자료 변경, 삭제도 동일

#### 2. 테이블 부풀림현상 확인 쿼리
- vacuum 을 통해 테이블을 정리할 필요가 있음.
```sql
select relname, n_dead_tup from pg_stat_user_tables;     --  vacuum 이 필요한 확인      
SELECT pg_size_pretty(pg_database_size('mydb'))          --  데이터베이스 용량 확인
SELECT pg_size_pretty(pg_relation_size('mytable'))       --  테이블 사이즈 확인         
SELECT pg_size_pretty(pg_index_size('mytable'))          --  인덱스 크기를 계산
SELECT pg_size_pretty(pg_total_relation_size('mytable')) --  테이블 사이즈 체크 시 연관 인덱스 사이즈 합산 
```

#### 3. vacuum 실행 명령어 
```sql
-- DB 전체 풀 실행                        vacuum full analyze;
-- DB 전체 간단하게 실행              vacuum verbose analyze;
-- 해당 테이블만 간단하게 실행     vacuum analyse [테이블 명];
-- 특정 테이블만 풀 실행              vacuum full [테이블명];
```
- verbose 옵션을 사용할 경우 n_dead_tup 은 줄어들지만 테이블 자체 용량은 줄어들지 않음    
- full 옵션을 사용할 경우 테이블과 DB 자체 용량을 줄일 수 있지만 DB 가 잠기기 때문에 문제가 생길 수 있음    
- autovacuum 설정은 옵션없는 표준 vacuum 과 동일함.    


## vacuum 작업 필요 이유
1. 변경 또는 삭제된 자료들이 차지 하고 있는 디스크 공간을 다시 사용하기 위한 디스크 공간 확보 작업이 필요하다.
2. PostgreSQL 쿼리 실행 계획기가 사용할 자료 통계 정보를 갱신할 필요가 있다.
3. 인덱스 전용 검색 성능을 향상하는데 이용하는 실자료 지도(visibility map, vm) 정보를 갱신하는 작업이 필요하다.
4. 트랜잭션 ID 겹침이나, 다중 트랙잭션 ID 겹침 상황으로 오래된 자료가 손실 될 가능성을 방지해야할 필요가 있다.


### 표준 vacuum 
- 기본적인 crud 가 실행되고 있어도 동시에 사용할 수 있음. / alter, create, drop 등은 불가능

### vacuum full
- 해당 테이블(혹은 DB)에 대해 배타적잠금을 지정하기 떄문에 어떤 작업도 불가능

### auto vacuum
- vacuum 은 디스크 입출력 부하를 만들어 동시 작업중인 다른 세션의 성능을 떨어뜨림
- autovacuum 사용 >  꾸준히 빈 공간을 확보해 디스크의 용량을 어느 정도까지만 커지게 해 vacuum full 을 해야하는 상황을 방지      
\>> full vacuum 을 진행하기엔 무리가 있음

# [autovacuum] 설정
## 1. 설정값 수정
```sql
alter system set autovacuum_vacuum_scale_factor = 0;
alter system set autovacuum_vacuum_threshold = 50;
```
## 2. vacuum 이 필요한 테이블 확인
```sql
select relname, n_live_tup, n_dead_tup from pg_stat_user_tables where n_dead_tup != 0;
```

### autovacuum 수행 기준
- n_dead_tup 수가 아래 값보다 클 경우 autovacuum 이 수행됨  
    `[ 존재하는 tuple 수 * (autovacuum_vacuum_scale_factor ) + autovacuum_vacuum_threshold ]`

### autovacuum_vacuum_scale_factor 를 0으로 setting 하는 이유
- 특정 테이블의 tuple 수가 많을 수록 한번에 많은 수를 vacuum 명령으로 처리하게 된다.    
 너무 많은 양을 처리할 경우 문제가 생길 수 있어 적당량으로 끊어줄 필요가 있다.

## 3. autovacuum 설정 적용
```sql
select pg_reload_conf();
```
- 다시 `select relname, n_live_tup, n_dead_tup from pg_stat_user_tables where n_dead_tup != 0;` 를 조회하면 값이 줄어든 것을 알 수 있음


## 4. DB용량 확인
```sql
select datname, pg_size_pretty(pg_database_size(datname)) from pg_database;
```
