## 복제

### 데이터 중심 어플리케이션 설계 5장 정리

복제가 필요한 이유
- 지리적으로 사용자와 데이터를 가깝게 유지해서 지연 시간을 줄임
- 가용성을 높이기 위해서
- 읽기 처리량을 늘리기 위해서

복제 알고리즘
- 단일 리더
- 다중 리더
- 리더 없는 복제

### 리더와 팔로워

리더가 로컬 저장소에 데이터를 저장하면 복제 로그가 팔로워들에게 전달된다

팔로워가 로그를 받으면 그 로그를 바탕으로 리더가 한 연산과 똑같은 과정을 통해 연산한다

### 동기식 복제 vs 비동기식 복제

- 동기식 복제의 장점 : 리더와 팔로워가 일관성 있게 데이터의 최신본을 가지는걸 보장함
- 동기식 복제의 단점 : 모든 팔로워에 쓰기가 성공했다는 응답을 기다려야 하기 때문에 그 전까지는 모든 쓰기 작업을 차단한다.
모든 팔로워가 동기식인 상황은 비현실적임

- 반동기식 복제 : 하나의 팔로워만 동기식으로 유지, 나머지는 비동기식 만약 동기식이 죽으면 비동기식 중 하나를 동기식으로 승격

보통의 리더 기반 복제는 **완전 비동기식**으로 구성한다.

- 완전 비동기식의 단점 : 만약 리더가 잘못된다면 아직 팔로워에게 동기화 하지 않은 데이터 모두 유실됨
- 완전 비동기식의 장점 : 모든 팔로워가 죽어도 쓰기 작업 진행할 수 있다.

### 새로운 팔로워가 추가됐을때, 그 팔로워가 최신 데이터를 가지게 하는 방법은? 

1. 리더의 현재 시점 스냅숏을 딴다
2. 스냅숏으로 새로운 팔로워에 복사한다
3. 복사가 진행되는 동안 발생한 변경분을 팔로워가 리더에게 요구한다
4. 변경 분 까지 다 반영하면 따라잡는다.

### 팔로워 장애
각 팔로워는 리더로부터 수신한 데이터 변경 로그를 로컬 디스크에 보관함
만약 팔로워가 장애가 났다가 복구되면, 장애 전 마지막 트랜잭션 체크해서 리더에게 미반영분을 다 받아온다

### 리더 장애

리더 장애시에는 팔로워 중 하나를 리더로 승격해야 한다
이 과정을 failover라고 한다.

수동 장애 복구와 자동 장애 복구가 있다

1. 리더가 장애인지 판단
2. 새로운 리더를 선택한다
3. 이전 리더가 자신을 팔로워로 인식할 수 있도록 시스템을 재설정한다

### 복제 지연 문제

비동기식 복제가 사용되기 때문에 리더와 팔로워에 동일한 질의를 했을때 다른 결과값이 잠시 나올 수도 있다.
하지만 이 일시적인 오류는 금방 해결된다 이를 최종적 일관성 이라고 한다.

### 자신이 쓴 내용 읽기

데이터를 수정 후, 바로 자신의 데이터를 조회하는 로직을 실행하면 최종적 일관성으로 인해 복제 서버에서 읽어올때 데이터가 업데이트 되지 않을 수도 있다.
이때 쓰기 후 읽기 일관성이 필요하다.

사용자가 데이터 재출 후 재로딩 했을때 무조건 자신이 쓴게 보여야 한다는 뜻임

해결법 : 
사용자가 수정한 내용은 팔로워가 아니라 리더에서 읽음


단조 읽기
두번의 쿼리를 날렸는데 첫번째는 데이터가 보였는데 두번째는 아직 안보일 수도 있다.

이 문제 해결하는 법 : 사용자 ID로 해싱하던지 해서 한명의 사용자가 하나의 팔로워로 조회를 계속 요청하도록 한다


### 다중 리더 복제

---



