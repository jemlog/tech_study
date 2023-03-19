## CORS

CORS(Cross-Origin Resource Sharing)란, 웹 브라우저에서 실행되는 JavaScript 코드에서 발생하는 보안 상의 이슈 중 하나다. 
보안 상의 이유로, 브라우저는 다른 도메인(출저)에 속한 자원(예: 다른 서버에 있는 이미지, 동영상, 스크립트 파일 등)을 자동으로 요청할 수 없다. 
이를 Same-Origin Policy(SOP)라고 한다.

### 같은 출저의 기준

> https://www.naver.com:443/mail 에서 443 포트까지가 같은 출저(도메인)이라고 부른다.

하지만, 웹 애플리케이션을 개발하다 보면, 다른 도메인에 있는 자원을 사용해야 하는 경우가 있다. 이를 위해 SOP에는 CORS라는 예외 조항을 둬서 SOP는 위반했더라도 CORS 지켰다면 다른 도메인에서도 데이터를 요청할 수 있도록 만들었다.

### CORS의 동작 원리

1. 웹브라우저가 서버로 요청할때 클라이언트의 출저를 담은 Origin 필드를 헤더에 포함해서 본 요청 이전에 Option 메서드를 통한 예비 요청을 보낸다.
2. 서버는 허용하고자 하는 출저 목록을 Access-Control-Allow-Origin 필드에 담아서 전송한다.
3. 웹브라우저는 서버에서 보낸 Access-Control-Allow-Origin 필드와 Origin 필드를 비교해서 위반 여부 판별한다.
4. 위반 여부를 기준으로 CORS를 위한 안했다면 본 요청을 보내고, 위반했다면 경고창을 띄운뒤 받아온 데이터는 버린다.

### Credential 기능

웹브라우저가 서버로 요청을 보낼때, 쿠키 같은 인증 값을 넣어보낼 수 있다. 하지만 기본적으로 인증 데이터는 same-origin일때만 넣을 수 있도록 설정되어있다.

사용하기 위해서는 자바스크립트 단에서 credentials라는 옵션을 변경해줘야 한다.

```javascript
fetch('https://jemlog.github/readme', {
  credentials: 'include', // Credentials 옵션 변경!
});
```

이때 서버단에서 지켜줘야 하는 규칙들이 있다.

- Acess-Control-Allow-Origin을 *로 설정하면 안되고 구체 url로 설정해야한다.
- Access-Control-Allow-Credentials: true를 서버측에서 응답 헤더에 꼭 넣어줘야 한다.


CORS는 다른 도메인 간의 자원 공유를 보다 쉽고 안전하게 할 수 있도록 도와주는 기술이다. 
하지만, 이를 잘못 구성하면 보안 상의 문제가 발생할 수 있으므로, 적절한 설정이 필요하다.
즉, 웬만하면 Access-Control-Allow-Origin을 * 로 설정하지 말고 필요한 Origin들만 등록해주자