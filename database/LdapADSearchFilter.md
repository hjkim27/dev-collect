---
title : ldap ad search query
date : 2023
type : database sync
---

# AD(Active Directory)
- Microsoft에서 개발한 디렉터리 서비스
- 중앙 집중형 데이터베이스에 사용자 정보 등이 저장되어 있음
- LDAP과 같은 표준화 프로토콜을 사용해 네트워크 관련 서비스를 제공하며 DNS 기반 서비스도 제공
- [AD(Active Directory)란 무엇인가?](https://m.blog.naver.com/quest_kor/221487945625)

# LDAP(Lightweight Directory Access Protocol)
- 원래는 전자 메일, 프린터, 주소록과 같은 응용 프로그램에서 서버의 정보를 조회하는데 사용
   - 이메일과 연락처를 검색하거나 네트워크상에서 프린터를 찾는 등의 검색에 이용
   - LDAP 클라이언트는 LDAP 서버에 정해진 프로토콜대로 정보를 요청하여 이 정보는 레코드(record)의 집합으로 구성된 디렉터리에 있음.

# AD 와 LDAP은 다른 개념이며 같이 사용되는 것이 일반적
- AD 는 디렉토리 공급 서비스, LDAP 은 AD 및 OpenLDAP 과 같은 디렉토리 서비스를 사용하는 응용 프로그램의 프로토콜 정의
- i.e. AD는 사용자 정보 데이터베이스 역할, LDAP은 사용자 인증이 필요한 애플리케이션들이 디렉토리에 어떻게 접속해 어떻게 인증을 수행하는지에 대한 기술적 정의

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
