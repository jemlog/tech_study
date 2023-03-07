## Tomcat 내부 분석

### Connector

톰캣은 기본적으로 HTTP 통신에 사용하는 하나의 Connector를 포함한다. 8080 포트를 통해 오는 요청들을 기다림

### Engine 

하나의 Engine은 하나 이상의 Host를 포함해야 한다. 톰캣 초기 설정은 host localhost를 포함하는 engine Catalina engine

catalina engine은 HTTP connector를 통해서 오는 모든 요청을 다루고, 대응하는 응답으로 돌려준다.

### Service

톰캣 초기 설정 : HTTP Connector와 Catalina Engine을 묶어주는 Catalina Service를 설정

### Server 

하나의 Engine과 다수의 커넥터를 포함하는 여러 Service를 포함하는 단위임

Catalina == Servlet Container

Coyote == Connector Framework


Connector의 역할
1. Port를 listen 해서 Socket Connection을 얻는다
2. Socket Connection으로부터 데이터 패킷을 받는다
3. 데이터 패킷을 파싱해서 ServletRequest로 만든다
4. ServletRequest를 알맞은 Servlet Container로 보낸다

Tomcat의 Connector에는 여러 종류가 있다

![스크린샷 2023-03-07 오전 12.50.53.png](..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-03-07%20%EC%98%A4%EC%A0%84%2012.50.53.png)

톰캣 9.0 버전부터 BIO Connector는 Deprecated 되었다. 최근에는 NIO Connector와 NIO2 Connector가 사용되고 있다.

### BIO Connector

BIO Connector는 JAVA 기본 I/O를 사용한다. 
즉, Socket Connection을 처리할때 Thread Pool에서 하나의 쓰레드를 가져와서 요청을 할당하고, 응답이 완료된 후에 Thread Pool로 복귀한다. 
Connection이 닫힐때까지 하나의 Thread가 계속 할당되는 것이다.

이 방식의 문제

- Thread들이 충분히 사용되지 않고 유휴 상태에 머무는 시간이 길어진다.
- 쓰레드 풀의 쓰레드 개수 만큼만 커넥션을 받을 수 있다.

이를 해결하기 위해 NIO Connector가 등장했다.

![스크린샷 2023-03-07 오전 1.01.43.png](..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-03-07%20%EC%98%A4%EC%A0%84%201.01.43.png)

Acceptor

- Socket Connection을 받는다

Worker

- Acceptor는 쓰레드 풀에서 유휴상태인 쓰레드를 찾는다. 만약 남는 쓰레드가 없다면 Acceptor는 Block 된다

Mapper

- 요청을 처리해줄 수 있는 Servlet을 매핑시킨다.

CoyoteAdapter

- HTTP 요청를 그에 상응하는 HttpServletRequest로 변환하고 적절한 Container에 바인딩 시킨다
- 요청의 JSESSIONID 값에 따라 서버 내의 session pool 속에 있는 session object 찾아서 HttpServlet Object에 넣어준다.

> Connector 내부에서 각 요청에 상응하는 Thread를 Worker Thread Pool에서 꺼내서 1대1로 매핑해준다.


### NIO Connector

![스크린샷 2023-03-07 오전 1.09.31.png](..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-03-07%20%EC%98%A4%EC%A0%84%201.09.31.png)

기존의 BIO Connector의 경우 새로운 Socket Connection이 발생하면 바로 새로운 쓰레드를 Worker Thread Pool에서 꺼내서 할당해줌. NIO Connector의 경우 중간에 Poller라는 쓰레드에 Connection을 넘겨준다.

Poller는 Socket들을 여러개 가지고 있다가 Socket이 데이터를 처리할 수 있을 때만 Thread를 할당해준다.

ex> socket이 readable한 상태, 즉 클라이언트로부터 데이터를 전달 받아 커널 버퍼에 입력한 후 실제로 worker Thread를 할당해준다.

해당 방식을 사용하면 Thread가 I/O 작업으로 인해 IDLE 상태가 되는 시간을 최소한으로 줄여준다.


NioEndPoint 내부에는 
- Socket Request Listener Thread인 Acceptor
- Socket NIO Thread인 Poller
- Request Processing Thread인 worker Thread (Thread Pool)
존재한다

**NIO EndPoint 내부 동작**

Acceptor가 serverSocket.accept()로 커넥션을 얻는다. 
accept과정을 통해 얻은 socket(channel)은 PollerEvent로 감싸져서 Poller EventQueue에 큐잉된다

NIO Connector는 Selector 기반으로 동작한다. Poller 내부에는 Selector가 들어있다.
Poller는 PollerEventQueue에서 PollerEvent를 얻어온 뒤에 PollerEvent 속 Channel을 Selector에 등록한다
Selector는 select 동작을 수행하고 데이터가 준비된 채널이 등장하면 Worker Thread Pool에서 유휴 Thread를 하나 가지고 와서 해당 Channel에 할당해준다.

Worker Thread가 소켓을 넘겨 받으면, Http11ConnectorHandler로부터 Http11NioProcessor를 받고 내부에서 CoyoteAdapter를 호출한다. 이후 과정은 BIO와 동일 (ex> JSESSIONID를 통해서 Session object가져오기) 이후 HttpServlet Object로 변환, Dispatcher Servlet에 요청 전달  -> 작업 완료 후 가지고 있던 소켓 통해서 클라이어늩에게 응답해줌

여기서 만약 요청이 쓰레드 풀 보다 크다면, maxThread까지 쓰레드 추가 생성해서 커버 가능, 만약 그래도 부족하면 server socket의 acceptCount 즉, 작업 대기 큐 만큼 대기, 그마저도 안되면 connection refuse 됨