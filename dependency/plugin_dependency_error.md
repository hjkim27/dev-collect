---
title : plugin dependency error
date : 2024.12.31
occurred : intellij maven build error
jdk : zulu23
---

### error

- jdk 버전 변경 후 spring-boot-starter 관련 의존성의 version 변경 진행
  
  |groupId | artifactId | version | new Version | 
  |--------|------------|---------|-------------|
  |org.springframework.boot | spring-boot-starter-parent  | 2.6.2 | 3.4.1 |
  |org.mybatis.spring.boot  | mybatis-spring-boot-starter | 2.2.2 | 3.0.4 |

- jstl 의 경우 groupId, artifactId 가 변경되었음
    
  |     |groupId | artifactId | version | 
  |-----|--------|------------|---------|
  | old |javax.servlet            | jstl                         | 별도 기입 X |
  | new |jakarta.servlet.jsp.jstl | jakarta.servlet.jsp.jstl-api | 3.0.2 |
  
해당 과정에서 아래 jar 를 변경하였더니 에러 발생

```
Cannot resolve plugin org.springframework.boot:spring-boot-maven-plugin:3.4.1
```

### Sol.
아래와 같이 version을 명시해줌으로서 해결
```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <!-- 기존에는 버전 명시가 없었으나 추가. plugin 버전과 spring boot 버전을 맞춰줌 -->
  <version>${project.parent.version}</version>
</plugin>
```
