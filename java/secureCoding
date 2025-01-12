### 1. SQL injection 대응
```xml
insert into ${table} values (...);
```
### 2. nullPointer 역참조
```java
if(ids == null){
  throw new Exception("ids is null!");
}
```
  - session.getAttribute > array
  - request.getParameter
  - list foreach
  
### 3. 오류상황 대응 부재
### 4. 경로조작 및 자원 삽입 : 외부에서 입력받은 경로 순회 문자열(./\) 제거
```java
// 수정 전
File file = new File(path);
// 수정 후
String filePath = path.replaceAll("\\.", "").replaceAll("/", "").replaceAll("\\\\", "");
File file = new File(path);
```
### 5. 부적절한 예외처리
```java
// 수정 전
try {
   ....
} catch (Exception e) {
   log.error(e.getMessage());
}

// 수정 후
try {
   ...
} catch (RuntimeException e) {
   log.error(e.getMessage());
} catch (Exception e ) {
   log.error(e.getMessage());
}
```
### 6. 솔트 없이 일방향 해쉬 함수 사용
```java
// 수정 전
MessageDigest digest = MessageDigest.getInstance("sha-256");
String mk = toHexString(digest.digest(uid.getBytes()));

// 수정 후
MessageDigest digest = MessageDigest.getInstance("sha-256");
byte[] salt = "salt".getBytes(StandardCharsets.UTF_8);
digest.update(salt);
String mk = toHexString(digest.digest(uid.getBytes()));
```


