## 데이터 무결성 제약 조건

### 무결성
무결성은 데이터의 정확성, 일관성을 나타낸다. 즉, 데이터를 정확하고 일관되게 유지하는 것을 의미한다.

### 무결성 제약 조건

무결성 제약조건은 데이터베이스의 정확성, 일관성을 보장하기 위해 저장,수정,삭제 등을 제약하기 위한 조건을 말한다.

- 개체 무결성
    - 각 테이블의 기본키를 구성하는 속성은 not null, unique 여야 한다.

- 참조 무결성
    - 외래키 값은 null이거나 참조하는 테이블의 기본키 값과 같아야 한다. 즉, 부모 테이블의 기본키값이 아닌 값은 입력할 수 없다

- 도메인 무결성
    - 속성들의 값은 정의된 도메인에 맞는 값이여야 한다. ex 성별 컬럼은 남,여 만 가능하다

- 고유 무결성
    - 특정 속성에 대해 고유한 값을 가지도록 했다면 각 행이 가지는 그 속성값은 모두 유니크 해야 한다.

- null 무결성
    - 특정 속성에 대해 null값을 허용하지 않았다면 각 행이 가지는 그 속성값은 모두 not null이어야 한다.

- 키 무결성
    - 각 테이블은 최소한 한 개 이상의 키가 존재해야 한다.