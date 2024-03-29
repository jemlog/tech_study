## TCP vs UDP

HTTP1.1 : 한번의 TCP 커넥션에서 하나의 요청만 처리한 후, 연결을 끊는다. 요청이 올때마다 매번 연결을 위한 3 way handshake와 종료를 위한 4 way handshake를 진행해야 한다.

HTTP2 : 한번의 TCP 커넥션으로 여러 요청을 처리할 수 있게 만들었다.

HTTP1.1에서 HTTP2로 넘어갈때는 단순히 핸드셰이킹 과정을 최소화 시킴으로써 레이턴시를 줄임. 반면 HTTP3는 UDP를 사용함으로써 핸드셰이킹 과정을 날려버림

HOLB (head of line blocking) : 특정 요청에 병목이 생겨서 전체적으로 레이턴시가 증가하는 상황을 의미한다.

TCP의 경우 통신 중간에 패킷이 유실되면 제대로 조립할 수 없기 때문에, 송신측은 무조건 수신측이 데이터를 모두 받았다는 신호를 받아야 한다. 만약 누락된 데이터가 있다면 재전송 해서 채워넣어야 한다. TCP는 반드시 정해진 순서대로 패킷을 조립해야한다. 패킷 처리 순서 정해져 있어서 이전 패킷 처리 전까지는 그 다음 패킷 처리 안됨

정리 : 
- 통신 중 패킷이 유실되는 상황
- 수신측의 패킷 파싱 속도가 느린 상황

이 상황들에서는 전체적인 레이턴시가 증가하고, 이를 HOLB라고 말한다.

TCP의 문제이기 때문에 HTTP1.1와 HTTP2가 둘 다 가지고 있는 문제이다.

### HTTP3가 사용하는 프로토콜, QUIC

QUIC은 TCP가 가지고 있는 이런 문제들을 해결하고 레이턴시의 한계를 뛰어넘고자 구글이 개발한 UDP 기반의 프로토콜이다.

QUIC의 목적은 TCP의 핸드쉐이크 과정을 최적화 하는데 초점을 맞춤

### UDP

UDP는 TCP와 다르게 패킷들 간의 순서가 중요하지 않다. 또한 어떤 경로를 거쳐서 도달했는지 신경쓰지 않는다.

UDP의 장점 : 커스터마이징이 가능하다.

TCP의 경우 신뢰성 있는 연결과 혼잡 제어 등을 위해서 TCP 프로토콜 내부적으로 많은 기능들을 가지고 있다. 

UDP 프로토콜은 출발지, 목적지, 체크섬, 패킷 길이 정도만 헤더에 포함되어 있다. 나머지는 모두 커스터마이징 할 수 있는 공간이고, 애플리케이션에서 어떻게 구현하는지에 따라 다양한 기능들을 가지게 될 수 있다.

TCP의 신뢰성 확보를 위한 방법은 필수 과정이기에 커스터마이징이 거의 불가, 레이턴시를 줄이기위한 노력을 하기 힘들다.

### UDP의 장점

연결 설정 시 레이턴시 감소

TCP+TLS의 경우 신뢰성 있는 통신과 암호화에 필요한 정보 모두 주고받은 뒤, 실제로 데이터를 주고받음
UDP의 경우에는 첫 연결 요청 보낼때 데이터도 같이 보냄

UDP 커넥션에 한 번 연결 성공하고 나면 설정 캐싱했다가 다시 오청 들어오면 바로 연결해준다 =

TCP syn 패킷의 최대 크기는 1460byte, 하지만 UDP는 데이터그램을로 첫 연결부터 보내기때문에 속도가 더 빠르다고 할 수 있다.

### ARG 
문제가 발생했을때, 재시도를 통해서 해결하려는 기법

TCP는 여러 ARQ 방식 중에서 Stop and Wait ARQ 방식을 사용하고 있다. 

Stop and Wait -> 송신측이 데이터를 보내고 나서 타이머 잼 -> 정해진 시간 안에 데이터를 반환하지 못하면 패킷 유실로 판단하고 재시도 한다. 

TCP의 경우 소스의 포트와 IP, 목적지의 포트와 IP를 사용해서 연결을 식별함

따라서 중간에 IP가 변경될 경우, TCP 연결이 끊어진다. 그렇다면 다시 3 way handshake과정을 거쳐야 하고, 만약에 모바일 환경 같은 곳에서 IP가 자주 바뀌는 환경에서는 전체적으로 레이턴시가 증가할 수 있다.

UDP를 사용하는 퀵의 경우, CONNECTION ID라는 랜덤한 값을 사용한다. 이는 클라이언트의 IP와는 전혀 관련이 없기 때문에 IP가 변경된다 하더라도 연결이 끊어지지 않는다.

HTTP2 부터는 멀티플렉싱을 지원한다. 단일 커낵션 내에서 여러개의 스트림을 사용해서 여러 데이터를 보낼 수 있다. 하나의 스트림에서 패킷이 손실되도 다른 스트림에는 영향을 미치지 않는다.



