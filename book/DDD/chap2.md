## 아키텍처 개요

**인프라스트럭쳐 계층에 의존시 문제점**
- 테스트의 어려움
- 기능 확장의 어려움

이에 대한 해결법은 DIP이다

고수준 모듈
- 의미있는 단일 기능을 실행하는 모듈
- 고수준 모듈은 여러 저수준 모듈에 의존한다
- DIP는 저수준 모듈이 고수준 모듈에 의존하도록 만든다.

고수준 모듈의 입장에서 저수준 모듈의 종류가 무엇인지는 전혀 중요하지 않다
인터페이스는 고수준 모듈에 속함. 행위 자체만 지정하기 때문이다

**DIP의 장점 : 테스트에 용이하다**
- 만약 고수준 모듈이 저수준 모듈의 구체 클래스에 의존했다면, 실제 구체 클래스가 만들어지기 전까지는 테스트를 실행할 수 없다.
하지만 인터페이스를 사용한다면 그 인터페이스를 의존하는 대역 객체를 주입하기만 하면 된다. 저수준 모듈들을 목으로 만들고 고수준 모듈의 로직을 테스트 하는 것!

**주의할점**
- DIP를 구현할때는 고수준 모듈의 관점에서 인터페이스를 도출해야 한다. 즉 비즈니스적 가치가 있는 행위를 도출해서 인터페이스에 녹여야 한다.

### 도메인 영역
도메인의 주요 개념을 표현, 핵심 로직을 구현

- 엔티티 : 고유의 식별자를 갖는 객체로 자신의 라이프 사이클 가짐
- 밸류 : 고유의 식별자를 가지지 않고, 개념적으로 하나인 값을 표현할때 사용한다
- 애그리거트 : 연관된 엔티티와 밸류를 개념적으로 하나로 묶는 것
- 도메인 서비스 : 도메인 로직이 여러 엔티티와 밸류를 필요로 하면 도메인 서비스에서 로직 구현, 응용 계층과는 다르다

DB 도메인과 도메인 모델의 차이점
- 도메인 모델은 구성 데이터 뿐만 아니라 도메인 로직을 포함한다

### 애그리거트

도메인 모델이 복잡해지면 전체 구조가 아닌 한 개 엔티티와 밸류에만 집중해서 큰 틀에서 모델 관리를 할 수 없게 됨

애그리거트 : 관련 객체를 하나로 묶은 군집

애그리거트 내부에는 모든 객체를 관리하는 루트 엔티티가 있다

클라이언트는 애그리거트 루트를 통해서 간접적으로 애그리거트 내의 다른 엔티티와 밸류 객체에 접근한다. -> 애그리거트 단위로 캡슐화 시켜줌



### 인프라스트럭쳐

스프링의 @transactional이나 @entity의 경우 어쩔 수 없이 저수준에 의존하게 한다. 하지만 dip는 무조건적인 정답이 아니라 구현의 편리함과 비교해서 효과가 더 클때 적용하는 것이다 

**중요한점**

패키지의 경우 단순 도메인이 아닌 애그리거트 단위로 나눈다. 만약 하나의 애그리거트에 2개의 하위 도메인이 포함될 경우 도메인 계층을 2개로 분리하면 된다.