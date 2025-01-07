---
title: dwr
date: 2023
type: java framework
---

# DWR (Direct Web Remoting)
-  프록시 기반 Ajax 로 Javascript 에서 Java Class 의 메서드를 사용할 수 있도록 해주는 프레임워크

## 필요 이유
1. 자바스크립트는 브라우저와 같은 클라이언트 단에서 동작
2. 자바로직은 서버 단에서 동작
3. 처리하는 방법은 Ajax 를 통해 다른 jsp에서 자바 로직을 처리하고, 콜백해서 처리
4. 위와 같은 과정을 좀 더 편리하게 해 줄 수 있어 보임

** 현재는 잘 사용하지 않음

## lib
```xml
<!-- https://mvnrepository.com/artifact/org.directwebremoting/dwr -->
<dependency>
    <groupId>org.directwebremoting</groupId>
    <artifactId>dwr</artifactId>
    <version>3.0.2-RELEASE</version>
    <type>pom</type>
</dependency>
```

## dwr로 사용중이던 ajax 제거 후 변경
  1) 값 전달 오류 : list 로 된 값이 jquery 에서 controller 로 전달되지 않음 > dataType: "json" 추가
  2) url 오류 : 호출 url에서 shpc 가 같이 출력되지 않음 > ${contextPath} 추가
  3) return 오류 : controller 에서 값을 반환하지 못해 error 로 던져짐 > @ResponseBody, @RequestParam 추가


#### 수정 전 : service.메서드 바로 호출
```script
ajaxClientService.deleteClients(clientId, {
    callback: function (data) {
        if ( clientId.length == data ) {
            alert("선택된 사용자를 삭제하였습니다.");
        }
        else if ( data == 0 ) {
            alert("사용자 삭제에 실패하였습니다.");
        }
        else {
             alert("일부 사용자를 삭제하지 못했습니다.");
        }
        showClientList();
    },
    async: false
});
```

#### 수정
** controller 
```java
@ResponseBody
@RequestMapping(value = "/dwr/deleteClients.ctr", method = RequestMethod.POST)
public Integer deleteClients(HttpServletRequest request, @RequestParam("clientId[]") List<Integer> clientId)  {
    int result = pcScanClientService.deleteClients(request, clientId);
    return result;
}
```
** ajax
```script
jQuery.ajax({
    url: "${contextPath}/dwr/deleteClients.ctr",
    type: "POST",
    async: false,
    data: {clientId: clientId},
    dataType: "json",
    success: function(data){
        if ( clientId.length == data ) {
            alert("선택된 사용자를 삭제하였습니다.");
        }
        else if ( data == 0 ) {
            alert("사용자 삭제에 실패하였습니다.");
        }
        else {
            alert("일부 사용자를 삭제하지 못했습니다.");
        }
        showClientList();
    },
    error: function(e){
        console.log(e);
    }
});
```

---
1) dto 객체 전달 오류
   - @RequestBody : 메서드는 정상적으로 리턴되는데 값이 전달안됨
   - @ModelAttribute : 객체 정상 정달 확인 완료
** ajax에서 data를 보내는 형식이 잘못되어 있었음
> 다른 ajax들의 경우 list, string, int 형 데이터로 각각 보내므로 data: { a:a , b:b , c:c } 의 형태로 데이터를 보내지만
  dto객체의 경우 별도 처리 없이 data: dto 로 보내면 @ModelAttribute가 자동으로 연결해줌

2) admin 추가/수정 오류
  - 추가/수정이 동일한 메서드를 타는데 추가할 경우에만 오류 발생    
 > adminInfoMapper.insert에서 오류가 발생하는것으로 확인    
 > 쿼리에서 오류가 나는 것으로 확인되는데 단순 insert 문으로 오류가 발생할 여지가 없음(오타없음)    
 > 확인 결과, insert 안에서 사용하는 selectKey에서 오류가 발생중    
   : 새로 DB를 생성했으므로 currval을 사용할 경우 현재값을 알 수 없어 오류가 발생중. (DB를 새로 떠와서 확인하면서 생긴 오류.)    
```sql
    <selectKey resultType="java.lang.Integer">
        select currval('admin_info_id_seq')
    </selectKey>
```
\> select nextval('admin_info_id_sql') 을 psql에서 한 번 실행 후 정상동작 확인 완료

