---
title : ldap ad search query
date : 2023
type : database sync
---

# AD서버
- 사용자가 마이크로소프트 IT 환경에서 업무를 수행하는 데 도움을 주는 데이터베이스이자 서비스 집합
- [AD(Active Directory)란 무엇인가?](https://m.blog.naver.com/quest_kor/221487945625)

 AD연동 관련 정리    
- 검색조건, 각 입력 필드가 어떻게 사용되는지 확인    
** adsync 에서 입력하는 query는 사용자 검색 쿼리가 아닌 특정 테이블을 칭함.     
** 해당 테이블에서 검색을 위한 쿼리는 하드코딩되어있음.     
  > 일반적인 검색조건으로 하드코딩되어있어 해당 고객사에서 어떤 식으로 테이블을 관리했는지 확인 필요    
  > 현재 검색 쿼리는 사용자일 경우 조회가 되도록 되어있음.
  > 
** 해당 AD 에서 objectClass와 objectCategory 확인 후 쿼리 재작성 필요. > 쿼리 수정 후 동작 확인 예정

## searhFilter
|  쿼리 입력 시 반환되는 항목 |  LDAP 검색 쿼리 |
|---|---|
| 모든 개체 <br> 참고: 로드 문제가 발생할 수 있음  |  objectClass=* |
|  'person'으로 지정된 모든 사용자 개체 | (&(objectClass=user)(objectCategory=person))  |
|  메일링 리스트만 | (objectCategory=group)  |
| 공개 폴더만  |  (objectCategory=publicfolder) |
| 기본 이메일 주소가 'test'로 시작하는 개체를 제외한 모든 사용자 개체  |  (&(&(objectClass=user)(objectCategory=person))(!(mail=test*))) |
| 기본 이메일 주소가 'test'로 끝나는 개체를 제외한 모든 사용자 개체  |  (&(&(objectClass=user)(objectCategory=person))(!(mail=*test))) |
| 기본 이메일 주소에 'test'라는 단어가 포함된 개체를 제외한 모든 사용자 개체  |  (&(&(objectClass=user)(objectCategory=person))(!(mail=*test*))) |
|  'person'으로 지정되고 그룹 또는 배포 목록에 속하는 모든 사용자 및 별칭 개체 |  (\|(&(objectClass=user)(objectCategory=person))(objectCategory=group)) |
| 'person'으로 지정된 모든 사용자 개체, 모든 그룹 개체, 모든 연락처를 반환하되, 값이 'extensionAttribute9'로 정의된 개체 제외  | (&(\|(\|(&(objectClass=user)(objectCategory=person))(objectCategory=group))(objectClass=contact))(!(extensionAttribute9=*)))  |
|  'CN=Group,OU=Users,DC=Domain,DC=com'의 값을 가진 DN으로 식별되는 그룹의 회원인 모든 사용자 |  (&(objectClass=user)(objectCategory=person)(memberof=CN=Group,CN=Users,DC=Domain,DC=com)) |
| 모든 사용자 반환  | Active Directory: (&(objectCategory=person)(objectClass=user)) <br>OpenLDAP: (objectClass=inetOrgPerson) <br>HCL Domino: (objectClass=dominoPerson)  |
|  Domino LDAP 디렉터리에서 메일 주소가 'person' 또는 'group'으로 지정된 모든 개체 |  (&(\|(objectClass=dominoPerson)(objectClass=dominoGroup)(objectClass=dominoServerMailInDatabase))(mail=*)) |
|  Active Directory에 이메일 주소가 있는 모든 활성 상태의(사용 중지되지 않은) 사용자 | (&(objectCategory=person)(objectClass=user)(mail=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))  |
|  그룹 DN으로 정의된 Group_1 또는 Group_2의 회원인 모든 사용자 |  (&(objectClass=user)(objectCategory=person)(\|(memberof=CN=Group_1,cn=Users,DC=Domain,DC=com)(memberof=CN=Group_2,cn=Users,DC=Domain,DC=com))) |
| extensionAttribute1 값이 'Engineering' 또는 'Sales'인 모든 사용자  | (&(objectCategory=user)(\|(extensionAttribute1=Engineering)(extensionAttribute1=Sales)))  |
