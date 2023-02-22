## SQL Injection

서버에서 실행되는 SQL을 악의적으로 이용하는 공격

기존 SQL에 악의적인 SQL 구문을 삽입하여 데이터 탈취, 삭제 등을 할 수 있음

```java
$username = $_POST['username']; // admin
$password = $_POST['password']; // password' OR 1=1 --

$mysqli->query("SELECT * FROM users WHERE username='{$username}' AND password='{$password}'")
// SELECT * FROM users WHERE username='admin' AND password='password' OR 1=1 --'
```

웹 보안에서 가장 중요한 부분 : 문자열 필터링

SQL에서 특별한 의미 가지는 문자열 이스케이프 처리함
preparedStatement 사용한다

preparedStatement : 
placeHolder를 집어넣은 쿼리를 미리 DB에 보낸다
그 이후에 placeHolder에 해당하는 데이터를 따로 DB로 보냄

error based sql injection
일부러 에러 발생시키면서 원하는 정보 얻음
단편적인 정보 얻을 수 있음

blind sql injection
쿼리 결과의 참/거짓 정보를 가지고 원하는 정보 추론 가능하다.
union sql injection


