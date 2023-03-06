## 애그리거트 트랜잭션 관리

선점 잠금 : DBMS가 제공하는 레코드 단위 잠금을 사용해서 구현함
-> select for update 구문을 사용해서 원자적 연산을 제공한다

교착 상태는 사용자 수가 많을때 발생하고, 사용자 수가 많아지면 교착상태에 빠지는 쓰레드 수가 늘어난다.

이를 방지하기 위해서 최대 대기시간 타임아웃을 꼭 설정해야 한다

```java
Map<String,Object> hints = new HashMap<>();
hints.put("javax.persistence.lock.timeout",2000)

Order order = EntityManager.find(Order.class,orderNo,LockModeType.PESSIMISTIC_WRITE,hints)

@QueryHints({
        @QueryHint(name = "javax.persistence.lock.timeout",value = "2000")
})
이거를 붙여서 적용한다

```

비선점 잠금 : 만약 버전이 다른걸 수정하려고 하면 트랜잭션 내부에서 OptimisticLockingFailureException이 발생한다.

OptimisticLockingFailureException은 사용자도 예측하지 못할 정도의 동시적인 쓰기가 발생
사용자가 직접 버전 값 가져와서 form에 넘겨준 후, 요청시 같이 넘겨서 현재 헨티티의 버전과 비교 가능하다.

만약 애그리거트 내의 루트 엔티티 이외의 엔티티가 수정됐다면, 원래 루트 엔티티 버전 안올라간다. 하지만 애그리거트 내의 무엇이라도 변경되면 루트 엔티티의 버전을 증가시키는게 맞다


### 오프라인 선점 잠금

분산 락이랑 같은 개념. 이거는 MySQL을 사용한다면 네임드 락을 사용하고, mysql 아니면 직접 jdbc 쿼리를 통해서 작업, 아니면 레디스 통한 분산락 구현하기!

 

