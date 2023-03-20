## REST와 RESTFul

REST(Representational State Transfer)는 웹 상의 자원을 이름(URI)으로 구분하여, 해당 자원의 상태(Representational)를 주고 받는 방식을 말한다. REST는 웹의 기존 동작을 이용하며, HTTP 프로토콜을 따르는 모든 플랫폼에서 사용이 가능하다.

RESTful은 REST를 구현하는 웹 서비스의 아키텍처를 의미한다. RESTful은 다음과 같은 원칙들을 따른다.

1. 자원(URI) - 모든 자원은 고유한 ID인 URI를 가지고 있어야 한다.
2. 행위(HTTP Method) - HTTP 프로토콜을 따르는 GET, POST, PUT, DELETE 등의 메소드를 사용하여 자원에 대한 행위를 표현한다.
3. 표현(Representations) - 서버가 클라이언트에게 보내주는 자원은 JSON, XML 등과 같은 포맷을 사용하여 표현된다.
4. 자기서술적 메시지(Self-descriptive message) - 각 요청은 그 자체로 요청에 필요한 모든 정보를 가지고 있어야 한다. 즉, 요청에 대한 설명이 요청 메시지에 포함되어 있어야 한다.
5. 하이퍼미디어(Hypermedia) - 서버는 클라이언트에게 다음에 취할 수 있는 옵션에 대한 링크를 제공해야 한다. (HETAOS)

RESTful 아키텍처를 따르는 웹 서비스는 간단하고 확장성이 좋으며, 서로 다른 클라이언트 플랫폼에서도 사용이 가능하다.