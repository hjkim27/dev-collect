## @Transactional 사용 시 주의사항 (참고 : https://deveric.tistory.com/86 ) 

#### 1. @Transactional 어노테이션의 동작방식 
- @Transactional 포함된 메서드가 호출되면, TransactionalManager를 사용하여 트랜잭션을 시작
- 메서드가 실행하기 전 begin을 호출하고, 메서드가 종료된 뒤 commit을 호출, 비정상일 경우 Rollback

#### 2. @Transactional 어노테이션 사용시 주의사항
1) @Transactional 을 달아놓은 메소드가 동일한 클래스 내의 다른 메소드에 의해 호출된다면 트랜잭션이 정상 작동하지 않음
  - 한 클래스 내에서 여러 메소드를 내부적으로 묶어 사용하고 있는 메소드
    \> 구성요소 메소드에 @Transactional 를 달지 않고 구성요소를 묶고 있는 상위개념의 메소드에 @Transactional 을 달아주어야 함. 
  - 구성요소 메소드에 @Transactional 을 달아 주어 트랜잭션으로 관리 할 경우 rollback 이 정상적으로 작동하지 않는 경우가 발생함
2)  @Transactional 은 public 메소드에서만 정상 작동
3) Spring Transaction은 기본적으로 unchecked Exception(런타임익셉션)만 관리하며 checked Exception(IOException, SQlExcption)등은 관리하지않음
  
#### 처리방법
1) @Transactional(rollbackFor=Exception.class) 와 같이 설정하여 모든 Exception 발생시 rollback 이 발생하게 처리하는 방법
2) checked Exception 이 발생할 가능성이 있는 부분을 try ~ catch 로 처리하여    
   checked Exception 발생시 unchecked Exception 으로 예외를 바꾸어 던지게 처리)하여    
   Transaction의 관리대상으로 묶어서 처리하는 방법

```
private 한 메서드에서 트랜젝션 처리된 메서드를 호출할 경우 문제 발생 >> private 를 public 으로 변경하여 우선 처리함
***** 
usb 인증 과정에서 발견된 현상이지만, 정상적으로 usb 인증이 되었었다고 함.
에이전트에서 정보를 잘못 보냈을 경우 발생하는 것으로 예상함.
에이전트 확인 필요
```
