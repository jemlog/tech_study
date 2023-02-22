## Statement vs PreparedStatement

일반적으로 쿼리는 3가지 과정을 거쳐서 실행된다
1. 쿼리 구문 분석
2. 컴파일
3. 실행

- Statement의 경우 매번 쿼리를 실행할때마다 위의 3가지 과정을 진행한다.
- PreparedStatement의 경우 객체를 생성하는 시점에 쿼리 구문 분석과 컴파일을 하고 커맨드 객체를 만든다
- 내부에 있는 캐시에 SQL 문자열을 키로 커맨드 객체를 밸류로 해서 저장한다.
- 쿼리를 실행하는 execute 실행 시, 캐시에서 커맨드 객체를 꺼내서 사용한다.
  SQL 문은 프리컴파일 되어 PreparedStatement 오브젝트에 저장됩니다.

PreparedStatement는 처음에만 파싱과 컴파일 처리를 한다. 그리고 컴파일한 SQL 구문은 preparedStatement 내부에 캐싱해둔다.
이미 컴파일이 된걸 사용하기 때문에 바인딩 변수에 특정 문자열을 넣어도 SQL 구문으로써 의미가 있는게 아니라 단순 문자열로 처리된다.

PreparedStatement는 JDBCConnection이 생성하고, 내부적으로 translateSQL 메소드를 통해 SQL 문장의 특수 문자들을 이스케이핑(escaping) 처리합니다.
따라서 SQL 인젝션을 방어할 수 있다.

```java
    @Override
    public PreparedStatement prepareStatement(String sql) throws SQLException {
        try {
            int id = getNextId(TraceObject.PREPARED_STATEMENT);
            if (isDebugEnabled()) {
                debugCodeAssign("PreparedStatement", TraceObject.PREPARED_STATEMENT, id,
                        "prepareStatement(" + quote(sql) + ')');
            }
            checkClosed();
            sql = translateSQL(sql);
            return new JdbcPreparedStatement(this, sql, id, ResultSet.TYPE_FORWARD_ONLY,
                    Constants.DEFAULT_RESULT_SET_CONCURRENCY, null);
        } catch (Exception e) {
            throw logAndConvert(e);
        }
    }
```




Statement에는 보통 변수를 설정하고 바인딩하는 static sql이 사용되고 Prepared Statement에서는 쿼리 자체에 조건이 들어가는 dynamic sql이 사용된다. PreparedStatement가 파싱 타임을 줄여주는 것은 분명하지만 dynamic sql을 사용하는데 따르는 퍼포먼스 저하를 고려하지 않을 수 없다.

Statement에 대해 SQL Injection을 시도했을때 결과
H2 - Statement 인터페이스 SQL 삽입 공격 성공
MySQL - 쿼리 실행 중 SQLSyntaxErrorException 예외 발생
PostgreSQL - Statement 인터페이스 SQL 삽입 공격 성공