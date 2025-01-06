---
title : no space left on device error
date : 2023
occurred : tomcat catalina.out
jdk : jdk8u191
---


# DB 용량 초과 시 확인 에러 코드
## 확인 파일 :  `tomcat/logs/catalina.out`
디스크 용량이 다 차서 발생하는 문제로 확인    
용량 확보 후 정상 동작 확인 완료
```
--- Cause: org.postgresql.util.PSQLException: ERROR: could not extend file "base/207391/484754": No space left on device
  Hint: Check free disk space.
    at com.ibatis.sqlmap.engine.mapping.statement.GeneralStatement.executeUpdate(GeneralStatement.java:91)
    at com.ibatis.sqlmap.engine.impl.SqlMapExecutorDelegate.update(SqlMapExecutorDelegate.java:505)
    at com.ibatis.sqlmap.engine.impl.SqlMapSessionImpl.update(SqlMapSessionImpl.java:90)
    at org.springframework.orm.ibatis.SqlMapClientTemplate$10.doInSqlMapClient(SqlMapClientTemplate.java:413)
    at org.springframework.orm.ibatis.SqlMapClientTemplate.execute(SqlMapClientTemplate.java:209)
    ... 41 more
Caused by: org.postgresql.util.PSQLException: ERROR: could not extend file "base/207391/484754": No space left on device
  Hint: Check free disk space.
    at org.postgresql.core.v3.QueryExecutorImpl.receiveErrorResponse(QueryExecutorImpl.java:2102)
    at org.postgresql.core.v3.QueryExecutorImpl.processResults(QueryExecutorImpl.java:1835)
    at org.postgresql.core.v3.QueryExecutorImpl.execute(QueryExecutorImpl.java:257)
    at org.postgresql.jdbc2.AbstractJdbc2Statement.execute(AbstractJdbc2Statement.java:500)
    at org.postgresql.jdbc2.AbstractJdbc2Statement.executeWithFlags(AbstractJdbc2Statement.java:388)
    at org.postgresql.jdbc2.AbstractJdbc2Statement.execute(AbstractJdbc2Statement.java:381)
    at org.apache.commons.dbcp.DelegatingPreparedStatement.execute(DelegatingPreparedStatement.java:168)
    at com.ibatis.sqlmap.engine.execution.SqlExecutor.executeUpdate(SqlExecutor.java:81)
    at com.ibatis.sqlmap.engine.mapping.statement.GeneralStatement.sqlExecuteUpdate(GeneralStatement.java:200)
    at com.ibatis.sqlmap.engine.mapping.statement.GeneralStatement.executeUpdate(GeneralStatement.java:78)
    ... 45 more
```
