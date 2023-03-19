## SSL 계층

SSL 계층은 인터넷에서 데이터를 안전하게 전송하기 위한 프로토콜이다. SSL은 TCP/IP 스택 위에 구현되며 전송 계층과 응용 계층 사이에 위치한다.

SSL은 데이터의 안전성을 보장하기 위해 데이터를 암호화 하고, 인증 및 기밀성의 기능을 제공한다. SSL 사용하면 통신 중에 제 3자가 데이터를 가로채도 데이터가 암호화 되어있기에 인식할 수 없다. 즉, 데이터의 안전성이 보장된다.

**SSL 통신 절차**

SSL 계층은 SSL 핸드셰이크 프로토콜과 SSL 레코드 프로토콜로 구성된다.

- SSL 핸드셰이크 프로토콜 : 클라이언트와 서버 간의 SSL 연결을 설정하는 역할이다. 암호화 방식 및 인증서 정보를 교환한다.
- SSL 레코드 프로토콜 : 클라이언트와 서버 간에 실제 데이터를 전송하는 과정이다.

> SSL은 더 나은 기밀성을 제공하는 TLS로 대체되었지만, 아직 예전 웹사이트들은 SSL을 사용하기도 한다.


심화 학습 
- SSL 핸드셰이크 프로토콜
- TLS
