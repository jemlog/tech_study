## HTTP

### HTTP 버전별 차이

### HTTP3

HTTP3는 QUIC이라는 구글이 만든 프로토콜 위에서 동작한다. QUIC은 UDP를 기반으로 만들어졌다.

**HTTP3의 특징**

HTTP3는 UDP 기반의 프로토콜을 사용한다. UDP는 기존의 TCP에서 많은 시간을 잡아먹었던 handshaking 과정이 없다. 따라서 낮은 레이턴시를 가진다.

또한 HTTP3는 Connection Id를 사용해서 클라이언트와 커넥션을 맺는다. IP 기반으로 연결을 맺을 경우, 자주 네트워크가 변경되는 모바일 환경에서는 커넥션이 끊길
확률이 높다. 이는 3-way handshake가 발생할 가능성이 높아진다는 말이고 레이턴시의 증가로 이어진다.

HTTP3의 경우 IP와 무관한 랜덤한 값인 Connection Id를 사용하기 때문에 네트워크 변경에 영향을 받지 않고 클라이언트와 연결을 유지할 수 있다.



### HTTP 요청 응답 헤더

HTTP 응답 헤더 주요 항목
HTTP 버전, 응답 코드

- HTTP 버전 정보- HTTP 프로토콜에 정의된 응답코드

ex) HTTP/1.1 200 OK



Server

- 웹 서버의 정보를 명시

ex) Server : Apache/2.2.24



Location

- 응답코드 301, 302 리다이렉션 상태에서 위치 정보를 지정



Set-Cookie

- 클라이언트에 저장할 쿠키 정보를 지정

Expires : 만료일
Secure : HTTPS에서만 사용
HttpOnly : 스크립트에서 접근 불가
Domain : 같은 도메인에서만 사용
위에 것들을 함께 설정 가능



Expires

- 해당 리소스의 유효 일시를 지정



Allow

- 응답 코드 405 (Method Not Allowed) 상태에서 서버가 제공할 수 있는 HTTP 메서드를 지정
