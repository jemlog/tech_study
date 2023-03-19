## TCP Handshaking

TCP의 연결 지향적 특징으로 인해 연결 생성과 연결 종료 시에 매번 핸드쉐이킹 과정을 거친다.

### 3-way handshaking

3-way handshaking은 연결을 생성할때 필요한 과정이다.

![3-way handshaking.png](..%2F..%2Fimage%2Fnetwork%2F3-way%20handshaking.png)

1. 요청자가 SYN_SENT 단계에서 랜덤으로 생성한 SYN를 보낸다
2. 수신자는 SYN_RECV 단계에서 요청자의 SYN에 1을 더한 ACK와 랜덤한 SYN를 보낸다
3. 요청자는 ESTABLISHED 단계에서 수신자의 SYN에 1을 더한 ACK를 보낸다
4. 연결 생성 완료

### 4-way handshaking

4-way handshaking은 연결을 종료할때 필요한 과정이다. 연결을 종료하는 핸드쉐이킹이 따로 필요한 이유는 강제로 연결을 종료할 시,
연결이 끊겼다는걸 **상대방이 인지하지 못하고**, 미처 **전송이 완료되지 못한 데이터가 있을 수도 있기 때문이다**.

![4-way-handshaking.png](..%2F..%2Fimage%2Fnetwork%2F4-way-handshaking.png)

1. 요청자가 FIN_WAIT_1 단계에서 FIN과 ACK를 같이 보낸다.
2. 수신자는 CLOSE_WAIT 단계에서 요청자에게 ACK를 보내고 남은 데이터를 보내기 시작한다.
3. 요청자는 수신자의 ACK를 받은 후, 수신자의 데이터 전송이 끝날때까지 대기 한다.
4. 수신자는 데이터 전송이 끝날 경우 LAST_ACK 단계로 진입해서 요청자에게 FIN을 보낸다. 이후 요청자의 ACK이 올때까지 대기
5. 요청자는 FIN을 받고 TIME_WAIT 단계로 진입 후, 수신자에게 ACK 보낸다.
6. 연결 종료

> 4-way handshake에서는 수신측의 남은 데이터를 모두 전송할 수 있도록 half-open 기법이 사용된다는게 중요함. 요청자는 이때 수신 스트림만 열고 수신자의 남은 데이터 처리해준다. 