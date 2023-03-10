## 도메인 모델 시작하기

도메인 : 소프트웨어로 해결하고자 하는 문제 영역

개발에 앞서서 해당 도메인의 전문가가 요구하는 사항을 정확하게 파악하는 것이 중요하다

도메인 전문가라고 완벽한 의견을 제시하는 건 아니다. 따라서 도메인 전문가의 요구 사항에 대해 왜 이런 기능을 요구했는지에 대한 의문을 가져야 한다.

도메인 모델 : 특정 도메인을 개념적으로 표현한 것

도메인을 이해하려면 도메인이 제공하는 기능과 도메인의 주요 데이터 구성을 파악해야 함. -> 객체 모델이 도메인 모델링에 적합함

도메인에 따라 용어 의미가 결정되므로, 여러 하위 도메인을 하나의 다이어그램에 모델링 하면 안됨

결국 하위 도메인마다 별개의 모델을 만들어야 한다.

### 애플리케이션의 아키텍쳐

- 사용자 인터페이스 계층 : 사용자의 요청 처리하고 정보를 반환한다.
- 응용 계층 : 비즈니스 로직을 직접 구현하지 않음. 도메인 계층을 조합해서 기능을 실행
- 도메인 : 시스템이 제공할 도메인 규칙 구현
- 인프라스트럭쳐 : 데이터베이스나 메세징 시스템같은 외부 시스템의 연동 처리

도메인 규칙을 객체 지향 기법으로 구현하는 패턴 : 도메인 모델 패턴

핵심 규칙을 구현한 코드를 도메인 모델에 위치 시키면 규칙이 바뀔때도 다른 코드에 영향이 많이 가지 않는다.
-> 엔티티 내부에 핵심 업무 규칙에 대한 코드를 넣어라

도메인에 대한 이해도가 프로젝트가 진행되면서 올라가는 만큼, 모델이 변경되는 일이 빈번하게 일어날 수 있다. 따라서 처음부터 완벽한 도메인 개념 모델 작성은 불가능. 처음에는 개요 수준으로 개념 모델을 잡고 차후 개념 모델을 구현 모델로 점진적 발전 시켜야함.


도메인을 모델링할때 기본이 되는 작업
- 핵심 구성요소, 규칙, 기능을 찾기

모델은 크게 엔티티와 밸류로 구분 가능

**엔티티** 
- 식별자를 가진다
- 식별자는 불변이기 때문에 식별자로 hashcode, equals 만들 수 있음

**엔티티의 식별자 생성 방법**
- 특정 규칙 따라 생성
- UUID 사용
- 값을 직접 입력
- 자동 증가 시퀀스 (DB에 실제로 넣어야 값을 알 수 있다)

**밸류 (값타입)**
- 밸류 타입은 개념적으로 완전한 하나를 표현할 때 사용
- 밸류 타입을 위한 기능을 추가할 수 있음.
- 밸류 객체의 데이터를 수정할때는 부분 수정 보다는 새로운 밸류 객체를 만들어버린다
- 만약 밸류 객체 자체를 불변으로 못 만들면, 다른 곳에 주입할때 방어적 복사 해야함 
- 두 밸류 객체 비교할때는 모든 속성이 같은지 equals hashcode로 비교해야함

**엔티티 식별자를 밸류 타입으로 생성**
- 엔티티 식별자에 String이나 Long 사용하면 실제 의미를 부여하기 위해 컬럼 이름을 맞게 지어야함
- 밸류 타입 사용하면 그냥 id라고 지어도 의미 파악됨

**Setter 메서드 사용 지양하기**
- setter를 사용하면 도메인의 핵심 개념이나 의도를 코드에서 사라지게 함으로 지양하자, 도메인 지식이 코드로 반영되는걸 막는다
- 도메인 객체가 불완전하지 않게 만들기 위해서는 생성 시점에 필요한 데이터들 모두 다 받아야 한다.
- 생성자로 받는 시점에 필요한 데이터 올바른지, 순서상으로 맞는지 다 체크할 수 있다.

코드 작성할때는 도메인에서 사용하는 용어가 매우 중요함 이를 **유비쿼터스 언어**라고 한다.
- 도메인 전문가, 기획자, 개발자의 소통 과정에서의 모호함을 줄일 수 있음
- 도메인과 코드 사이에서 불필요한 해석 과정 줄여줌

**도메인 용어에 맞는 알맞는 단어를 찾기 위한 노력을 계속 해야함**

