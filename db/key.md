## 데이터베이스 키

---
### 키의 종류

슈퍼키

- 하나의 레코드를 유일하게 식별할 수 있는 속성들의 집합, 하나 또는 여러개의 속성들이 묶일 수 있다.


후보키
- 슈퍼키들 중 최소한의 속성을 가지고 있는 키를 말한다.

기본키
- 후보키들 중 선택된 하나의 키
- not null 해야 하고 unique 해야 한다.

외래키
- 다른 테이블의 프라이머리 키와 연결되는 테이블의 컬럼

대체키
- 프라이머리 키 이외의 후보키들을 말함

복합키
- 테이블에서 최소 조건인 하나의 컬럼만으로 기본키를 만들 수 없을때 2개 이상의 컬럼을 합쳐서 만들 수 있는 후보키

### 클러스터링 키 (MySQL InnoDB)


클러스터링 인덱스를 사용할 시의 장단점


- 장점
    - PK로 검색할 때 처리가 매우 빠름
    - 연속되는 PK로 조회할 경우 랜덤 I/O가 아닌 순차 I/O를 사용하여 처리 속도가 더욱 빠름
    - 인덱스가 PK값을 가지므로 인덱스로 PK 값만 조회하는 경우 효율적으로 처리될 수 있음(=커버링 인덱스)
- 단점
    - 모든 인덱스가 PK에 의존하므로 PK 값이 클 경우 전체적으로 인덱스의 크기가 커지고, 페이지 양이 많아짐
    - 인덱스를 통해 검색할 때 PK로 다시 한번 검색해야 하므로 처리 성능이 느림 → 세컨더리 인덱스 사용 시
    - INSERT 시에 PK에 의해 레코드의 저장 위치가 결정되기 때문에 처리 성능이 느림
    - PK를 변경할 때 레코드를 DELETE 및 INSERT 해야 하므로 처리 성능이 느림
    - PK를 변경하면 레코드의 물리적인 저장 위치가 변하기 때문에 인덱스도 수정이 필요함



클러스터링 키 지정

1. 기본적으로 PK를 클러스터링 키로 선택함
2. PK가 없다면 NOT NULL 옵션의 유니크 인덱스 중에서 첫 번째 인덱스를 클러스터링 키로 선택함
3. 후보군이 없다면 내부적으로 자동 증가 유니크 컬럼을 추가한 후 클러스터링 키로 선택함

❗️내부적인 자동 증가 컬럼은 사용자가 사용할 수 없으므로 효율이 떨어진다. PK는 꼭 따로 생성해주자.


### 기본키로 자연키 사용 vs 대체 키 사용


- 대체키는 보통 autoincrement로 숫자 사용
- 자연키는 문자열을 보통 사용 Ex. 주민등록번호

이때 문자열을 사용하면 PK를 비대하게 만들어 성능에 좋지 못하다. PK의 크기는 작을수록 좋으며, 원시 타입일수록 좋다. 보통 문자열이 숫자보다 처리 속도도 느리다.

자연키를 사용할때의 단점

- 테이블 구조가 복잡해짐
    - 자연키를 PK로 사용할 때, 1개의 컬럼 만으로 PK를 구성하지 못하고 복합키로 설정해줘야 하는 경우도 상당히 많다. 대체 키를 사용하면 테이블 구조가 단순해진다

대체키를 사용할때의 장점
- 무결성 검사를 DB로 위임 가능
    - 키의 생성 및 관리를 완전히 데이터베이스에 위임, 자연키를 사용하는건 키의 유효성 검사와 무결성 검사를 개발자가 직접 해줘야 한다.
