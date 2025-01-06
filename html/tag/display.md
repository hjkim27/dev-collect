---
title: display tag 를 일반 div, table 등의 태그로 변경할 경우 참고
date: 2023
type: html/tag
---

# `<display>` 제거 시 옵션 확인
   
|option | description |
|---|---|
|ellpadding | "padding" css 속성을 사용하는 것이 더 좋음|
|cellspacing | attribute에 전달되는 html|
|class  | attribute에 전달되는 html|
|decorator | TableDecorator에 대한 전체 수식어가 붙은 클래스 이름. <br> 전체 합계 구하기와 같이, 전체 목록에 대한 맞춤형 연산들을 제공하는데 TableDecorator를 사용 <br> org.displaytag.decorator.TableDecorator를 상속받아 구현|
|defaultorder | 정렬된 컬럼의 디폴트 순서. | 
|defaultsort | 정렬을 위해 디폴트로 사용될 컬럼의 인덱스(1에서 시작한다) |
|export| 내보내기를 이용 가능/불가능 하도록 함 (cvs, excel, xml 지원)|
|htmlId | attribute에 전달되는 html "id" |    
|length|    보여질 레코드들의 개수|
|list | 테이블에 대한 소스로서 사용되는 객체에 대한 참조. 당신은 (반복 출력할 목록 변수값) ( ${list} )|
|name | 테이블에 대한 소스로서 사용되는 객체에 대한 참조 ( ${list.property} )|
|offset | 보여지게 될 첫 번째 레코드의 인덱스|
|pagesize | 한 페이지 내에 있는 레코드들의 개수 |
|requestURI | 존재할 경우에, 정렬, 내보내기, 쪽매김에 대한 링크들은 어떤 태그 생성된 파라미터들을 requestURI 속성의 값에 추가함으로써 형성된다.|
|requestURIcontext | 어플리케이션 컨텍스트를 생성된 링크들로 심리하는 것(prepending)을 이용 가능/불가능 하게 한다. |
|sort | 정렬 기능 사용|
|uid | 표의 ID, 현재의 행을 표현하는 객체는 또한 이 이름으로 pageContext에 추가되고 현재의 행 번호는 uid_rowNum 키를 사용하여 추가된다. |
|autolink  |링크 추가하기|
