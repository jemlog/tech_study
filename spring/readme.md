## 스프링




### 스프링 프레임워크

자바 플랫폼을 위한 오픈소스 애플리케이션 프레임워크로서 엔터프라이즈급 애플리케이션을 개발하기 위한 모든 기능을 종합적으로 제공하는 경량화된 솔루션이다.

### Spring, SpringBoot 차이

**빌드 후 배포 방식**
- Spring의 경우 WAR 파일로 빌드 후 직접 WAS인 톰캣에 배포 시켜야 한다.
- SpringBoot의 경우 Executable JAR로 애플리케이션 코드와 내장 톰캣을 함께 빌드함으로 Main 메서드만 실행하면 WAS 위에서 동작한다.

**라이브러리 관리**

- Spring의 경우 프로젝트에 필요한 라이브러리를 직접 다 세팅해야 하고, 라이브러리와 스프링 간 버전 호환성을 하나하나 다 고려해줘야 한다.
- SpringBoot의 경우, Starter 팩을 사용해서 프로젝트에 필요한 라이브러리들을 자동으로 세팅해준다. 또한 스프링과 호환되는 라이브러리 버전을 알아서 세팅해준다.

**스프링 빈 등록**

- Spring의 경우 라이브러리를 다운 받더라도 프로젝트에 필요한 기능들은 하나하나 스프링 빈으로 스프링 컨테이너에 등록해줘야 한다. 
- SpringBoot의 경우, 자동 구성(AutoConfiguration)을 사용해서 조건에 따라 미리 스프링 빈 등록 코드를 라이브러리가 제공해줄 수 있다. 또한 스프링부트 자체적으로도 프로젝트에 자주 사용되는 구성들을 AutoConfigure starter를 통해 제공해준다.


![스크린샷 2023-03-07 오전 12.50.53.png](/image/springboot_1.png)

위 사진에 있는 autoConfigure 패키지에 AutoConfiguration 관련 코드가 모두 들어있다.

![스크린샷 2023-03-07 오전 12.50.53.png](/image/springboot_2.png)

다음 사진에 메타 에노테이션으로 붙어있는 @EnableAutoConfiguration이 자동 구성(AutoConfiguration)을 가능하게 해준다.

### Bean

스프링 컨테이너에 등록된 객체를 의미한다.

### Container

@Bean 애노테이션을 사용해서 스프링 컨테이너에 빈을 등록할 수 있다.

> @Configuration의 동작 방식에 대해 자세히 알아볼 필요가 있다.

흔히, 스프링 빈을 등록할때 이런 식으로 등록한다.

```java
@Configuration
public class JavaConfig {

    @Bean
    public JavaBean javaBean()
    {
        return new JavaBean();
    }
}
```
이때 @Configuration이 없이 @Bean만 있어도 스프링 컨테이너에 스프링 빈으로 등록된다.

@Configuration를 사용하게 되면 빈 등록 메서드에서 생성되는 객체를 싱글톤으로 보장해준다.

```java
@Configuration
public class JavaConfig {

    @Bean
    public JavaBean javaBean() {
        return new JavaBean();
    }
  
    @Bean
    public JavaBean2 javaBean2() {
        
        // 이 과정에서 javaBean() 메서드를 통해 새로운 객체가 생성되지 않도록 해준다
        return new JavaBean2(javaBean());
    }
}
```
이 과정은 스프링이 CGLIB를 통해 프록시를 생성하고, 프록시 내부에 싱글톤을 보장해주는 코드를 추가함으로써 진행된다.

> 만약 위의 케이스처럼 빈 등록 메서드가 2번 이상 호출되는 경우가 없다면, 프록시 생성은 리소스 낭비다. 따라서 @Configuration(proxyBeanMethods = false) 라는 옵션값을 통해 프록시 생성을 막는다.




### IOC(Inversion of Control)

---

프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다. 우리가 흔히 사용하는 스프링 컨테이너가 대표적인 IOC 컨테이너라고 할 수 있다.

```java
import java.util.List;

public class StockService() {
    
    // 클라이언트 객체가 스스로 필요한 저장소 객체를 생성하고 실행했다.
    private StockRepository stockRepository = new StockRepository();
    
    ...

    public List<Stock> getStockList()
    {
        return stockRepository.findAll();
    }
}
```

위의 코드에서는 StockService가 직접 StockRepository를 생성하고 사용했기때문에 제어권이 StockService에 있다.

```java
@Service
@RequiredArgumentConstructor
public class StockService(){
    
    // 클라이언트 객체가 직접 필요한 객체를 생성하지 않음
    private final StockRepository stockRepository;
    ... 
    
    public List<Stock> getStockList()
    {
        return stockRepository.findAll();
    }
}
```

반면 아래의 코드는 클라이언트 객체 내부에서 직접 객체를 생성하지 않았지만, stockRepository를 사용할 수 있다. 이는 stockRepository에 대한 생명주기를 스프링 컨테이너에서 관리하고 외부에서 주입해주기 때문이다.

IOC의 장점
- 어떤 구체 기술을 사용하는지에 대한 고민없이, 클라이언트 객체는 인터페이스만 호출해서 실행하면 된다.


### DI (Dipendency Injection)

애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라 한다. 클라이언트는 코드 내부에서 직접 사용할 객체를 생성할 필요가 없다.

> 스프링 컨테이너는 IOC와 DI 모두의 역할을 하기 때문에 IOC 컨테이너와 DI 컨테이너 둘다로 불릴 수 있다.

### DL(Dependency Lookup) - 의존성 검색

스프링 컨테이너에 등록되어 있는 스프링 빈을 직접 검색하는 방법을 의미한다.

**스프링 컨테이너를 통해 직접 검색**
```java
public class Test{
    
    @Autowired
    private ApplicationContext applicationContext;
    
    public void test(){
        applicationContext.getBean(JavaBean.class);
    }
}
```

**ObjectProvider를 통해서 검색**
```java
public class Test{
    
    @Autowired
    private ObjectProvider<JavaBean> objectProvider;

    public int test() {
        JavaBean javaBean = prototypeBeanProvider.getObject();
    }
}
```

**ObjectProvider 사용 이유**
- applicationContext.getBean()을 사용하려면 스프링 컨테이너 자체를 주입 받아야 한다. 따라서 만약 테스트를 진행한다면 테스트가 매우 무거워질 수가 있다.
- objectProvider.getObject()를 사용하면 직접 applicationContext를 주입 받을 필요 없이 필요한 빈을 검색할 수 있다.


### POJO

---

POJO는 말 그대로 해석을 하면 오래된 방식의 간단한 자바 오브젝트라는 말로서 Java EE 등의 중량 프레임워크들을 사용하게 되면서 해당 프레임워크에 종속된 "무거운" 객체를 만들게 된 것에 반발해서 사용되게 된 용어이다.

**POJO는 순수한 자바 객체로만 프로그램을 짜는걸 말하는가?**

-> POJO는 특정 기술에 의존하지 않음으로써 객체 지향의 장점인 다형성을 살리는 방식을 말한다.

POJO를 실천하지 못한 경우
- ORM을 사용할때 Hibernate에 직접적으로 의존하는 방법

POJO를 실천한 경우
- ORM 표준 인터페이스인 JPA를 사용하는 경우

> 인터페이스를 사용하게 되면 구현 기술의 변화에 영향을 받지 않기 때문에 확장성을 얻을 수 있다.

### DAO와 DTO

---

**DAO**

Data Access Object의 약자로 DB의 데이터에 접근하기 위한 객체를 말한다.

repository와 dao의 차이

repository는 엔티티 객체를 보관하고 관리하는 저장소, dao는 데이터에 접근하도록 db 접근 관련 로직을 모아둔 객체

주로 dao에는 JPA로는 처리하기 힘든 native SQL 쿼리들을 실행하는 메서드를 모아둔다고 생각하면 좋을 것 같다.

- DAO와 Repository는 비슷한 역할을 함
- Repository는 엔티티 객체를 다루고 관리하는 저장소, DAO는 SQL 기반 쿼리 실행 메서드를 모아놓은 객체

**DTO**

Data Transfer Object의 약자로 계층 간 데이터 교환 역할을 한다.

![스크린샷 2023-03-01 오후 2.37.57.png](..%2F..%2F..%2FDesktop%2F%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202023-03-01%20%EC%98%A4%ED%9B%84%202.37.57.png)

DTO를 사용하는 이유

계층간의 의존성을 제거하기 위해서 사용한다.
```java
@RestController
@RequiredArgumentContructor
public class StockController
{
    private final StockService stockService;
    
    // 응답 객체로 엔티티를 그대로 사용
    public List<Stock> getStockList()
    {
        return stockService.getStockList();
    }
}
```

위의 코드를 보면 레포지토리 계층에서 조회한 Stock 엔티티 객체를 컨트롤러 계층에서 그대로 반환하는걸 알 수 있다. 이때 처음에는 문제없이 동작하다가 클라이언트와의 상의 없이 엔티티의 필드 명을 변경하거나 임의로 삭제하게 된다면 API 스펙과 틀어져서 장애가 발생할 확률이 있다.

즉, 컨트롤러 계층이 레포지토리 계층의 변경사항에 의존적이게 되는 것이다. 서로 다른 계층의 변화에 의존적이지 않게 하려면 각 계층 전용 입력,출력 DTO를 만드는게 좋다.

### MVC 패턴

---

https://velog.io/@seongwon97/MVC-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80 (MVC 참고)

model
- 뷰로부터 사용자 요청이 들어오면 DB로부터 데이터를 얻어와 전처리 한 후 컨트롤러에게 전달해주는 역할을 함, model은 view에 종속적이면 안됨

view
- 사용자가 직접적으로 interaction 하는 화면을 생성함

controller
- model과 view를 이어주는 역할을 한다.


### MVC vs Webflux

webflux의 근간 : 이벤트 루프

### Filter와 Interceptor

- https://mangkyu.tistory.com/173 (필터와 인터셉터 차이)
- https://mangkyu.tistory.com/221 (필터를 스프링 빈으로 등록 및 주입 가능한 이유)

![스크린샷 2023-03-01 오후 2.37.57.png](/image/filter_1.png)

**Filter**

디스패처 서블릿에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가작업을 처리할 수 있는 기능을 제공한다. 스프링 컨테이너가 아닌 서블릿 컨테이너에서 관리한다.

```java
public interface Filter {

    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
```
- Filter의 경우 HttpRequest로 한정되지 않고 그보다 상위인 servletRequest를 받는다.
- doFilter는 filterchain을 사용해서 연쇄적으로 filter를 사용할 수 있다.



**Interceptor**

스프링이 제공하는 기술이다. 디스패처 서블릿이 컨트롤러를 호출하기 전과 후에 요청과 응답을 참조하거나 가공할 수 있다
```java
public interface HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {
        
        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        @Nullable Exception ex) throws Exception {
    }
}
```

prehandle
- 컨트롤러가 호출되기 전에 실행된다. 컨트롤러로 요청이 들어가기 전해 해야하는 전처리 과정이 포함된다.

PostHandle
- 컨트롤러 실행 후에 실행된다.컨트롤러 하위 계층에서 작업 진행하다가 중간 예외 발생하면 호출안됨

afterCompletion
- 모든 뷰까지 최종 결과를 생성한 후에 실행된다. 사용한 리소스 반환에 사용하기 좋은 중간에 예외 발생해도 반드시 호출된다

**필터와 인터셉터 차이**

필터의 사용처
- 공통된 보안 인증/인가 작업
- 모든 요청에 대한 로깅 및 감사
- 이미지 데이터 압축 및 문자열 인코딩
- spring과 분리되는 기능

공통적인 보안 작업 (xss) 등을 진행하여, 스프링 컨테이너 내부까지 전달되지 않도록 하는게 좋다. 웹 어플리케이션 전반적으로 사용되는 기능 구현에 적합

인터셉터의 사용처
- 세부적인 보안 및 인증/인가 공통 작업
- API 호출에 대한 로깅 감사


### AOP

### 스프링 MVC에서 많은 요청이 발생할 때 생기는 상황

### Spring JDBC를 통한 데이터 접근

### 톰캣 내부 구조

### 톰캣 기본 사이즈, 최대 사이즈

- Max Thread Size : 200
- Min Thread Size : 10

### N+1 문제

### 스프링 MVC에서 많은 요청이 발생할 때 생기는 상황 (다중 접속 처리)

**다중 접속 서버와 I/O Multiplexing**





스프링부트에 내장되어있는 **서블릿 컨테이너인 톰캣**에서 다중요청을 처리한다.

**톰캣과 쓰레드풀**

톰캣은 다중 요청을 처리하기 위해 미리 쓰레드 풀을 생성한다. 쓰레드 풀을 생성하지 않고 개별 요청마다 쓰레드를 생성하면 CPU나 JVM 같은 리소스에 큰 부하가 간다. 특히 동시에 수많은 요청이 몰리면 서비스가 다운될수도 있다.

**톰캣의 요청 처리 과정**

1. CPU Core size 만큼 쓰레드 풀을 만든다
2. 클라이언트의 요청이 들어오면 작업 큐로 들어간다
3. 유휴 상태의 쓰레드가 있다면, 작업 큐에서 요청을 꺼내서 쓰레드에 할당한다
4. 만약 유휴 상태의 쓰레드가 없다면 작업이 작업큐에 대기한다
5. 만약 작업큐가 다 차면 새로운 쓰레드 생성한다
6. Max-thread 도달하면 스레드를 새로 생성한다
7. 만약 작업 큐도 가득 차고, 쓰레드도 Max-Thread 만큼 생성됐다면 이후에 들어오는 요청은 Connection-Refused가 된다.
8. 정상적인 상황이라면 Core-size 이상의 쓰레드는 쓰레드 풀로 반환되지 않고 Destory 된다

**톰캣에서 제공하는 기본 설정값**
- Max-Thread : 200
- Idle-Thread : 25
- Max-connections : 8192 # 동시에 들어올 수 있는 커넥션 수 NIO에서 사용
- Accept-Count : 100 # 작업 큐 사이즈
- Connection-Timeout : 20000 # 타임아웃 20초

**스프링부트에서 제공하는 기본 설정값**
- Max-Thread : 200
- Idle-Thread : 10

위의 내용에 따르면 Max-Thread 개수를 채웠고 작업 큐도 가득찼다면 그 다음의 요청들은 Connection-Refused되야 한다. 하지만 실제 테스트 시 Connection-Refused는 발생하지 않는다.

이유는 Java BIO가 아닌 JAVA NIO를 사용하기 때문이다

**전통적인 JAVA IO vs JAVA NIO**

BIO의 경우, 하나의 요청에 하나의 쓰레드가 할당되고, 응답이 끝날때까지 해당 쓰레드가 계속 할당되어 있다. 이 방법은 요청 중간에 IO라도 발생하면 Thread는 일하지 않는 idle 타임을 길게 가져가야 한다는 것이다.
또한 쓰레드의 개수 만큼만 동시 요청을 받을 수 있다.

**JAVA NIO**

JAVA NIO는 Selector를 이용해서 여러 소켓 채널을 관리한다.
- Selector에 소켓 채널들 등록
- 채널에서 이벤트가 발생할때까지 selector가 대기
- 채널에 이벤트가 발생하면 그때 유휴 쓰레드를 할당

**Tomcat에서 Java NIO가 사용되는 곳**

NIO Connector

- Acceptor가 serverSocketChannel.accpet()를 통해 socketChannel 얻어냄
- socketChannel은 톰캣 NioChannel로 변환되고 PollerEvent로 한번 더 래핑
- PollerEvent Queue로 들어감

**Selector는 Poller라는 별도의 쓰레드가 관리**

NIO 방식의 장점 :
- 하나의 쓰레드가 하나의 요청에 1대1로 매칭되지 않기 때문에 쓰레드 개수에 비해 많은 커넥션을 처리할 수 있고 idle 타임을 줄일 수 있다.
- 작업 큐 크기와 상관 없이 많은 요청들을 connection-refused 시키지 않고 처리할 수 있다.
- Max-thread까지 쓰레드 늘려서 작업 가능

### SpringMVC vs Spring Webflux

**spring MVC**
- 사용자의 요청마다 스레드를 계속 생성해야 한다. 이때 쓰레드풀을 사용해서 이미 일정량을 확보해둔다.
- 많은 사용자가 동시요청을 보내면 쓰레드풀 고갈 현상이 발생한다. (동시에 많은 요청이 올때랑 이어지는 듯) 처리안된 요청들은 계속 큐잉됨
- 시스템 트래픽에 맞게 쓰레드풀 크기를 잘 설정해야 한다.

> 기존의 MVC 방식은 동시 트래픽 대량으로 처리하는데 취약하다. 따라서 webflux가 각광받고 있다.


**spring MVC**
- 동기적인 방식
- blocking 방식으로 구현

**spring webflux**
- 비동기적인 방식
- 논블록킹 방식으로 구현
### Mono, Flux

Spring WebFlux는 Spring 5에 새로 추가된 모듈이다.

Non-blocking에 reactive stream을 지원하는 특징을 가지고 있다.

reactive streams -> non-blocking에 back pressure를 사용한 비동기 데이터 처리 표준

Flux와 Mono는 Reactor 라이브러리의 주요 객체이다.

Flux와 Mono의 차이점 : 발행하는 데이터 개수

- Mono는 0 ~ 1개의 데이터를 전달한다
-


### Sync vs Async, Non-blocking vs Blocking

- 동기와 비동기 : 프로세스의 수행 순서 보장에 대한 메커니즘
- 블록킹과 논블록킹 : 프로세스의 유휴 상태에 대한 개념

동기의 의미 : 정확히 같은 시간에 발생, 존재하는 것, 동시에 똑같이 진행되는 느낌 , 현재 작업의 응답과 다음 작업의 요청

즉, 작업이 어떠한 순서를 가지고 진행된다

### Blocking

```javascript
function employee () {
  for (let i = 1; i < 101; i++) {
    console.log(`${i}번 수행`);
  }
}

function boss () {
  console.log('사장: 출근');
  employee();
  console.log('사장: 퇴근');
}

boss();
```

### Non-Blocking

```javascript
function* employee () {
  for (let i = 1; i < 101; i++) {
    console.log(`${i}번 수행`);
    yield;
  }
  return;
}

function boss () {
  console.log('사장: 출근');

  const generator = employee();
  let result = {};

  while (!result.done) {
    result = generator.next();
    console.log(`사장: 유튜브 시청...`);
  }

  console.log('사장: 퇴근');
}

boss();
```

### Async

동기 방식에서 상위 프로세스는 작업을 시킨 하위 프로세스의 작업 상황을 계속 신경쓰고 있어야 한다. 하위 프로세스의 작업이 끝나야 그 다음 상위 프로세스의 작업을 실행할 수 있다.

```javascript

function employee (maxDollCount = 1, callback) {
  let dollCount = 0;
  const interval = setInterval(() => {
    if (dollCount > maxDollCount) {
      callback();
      clearInterval(interval);
    }
    dollCount++;
    console.log(`${dollCount}번 수행`);
  }, 10);
}

function boss () {
  console.log('사장: 출근');
  employee(100, () => console.log('직원: 눈알 결산 보고'));
  console.log('사장: 퇴근');
}

boss();
```

비동기 방식이기 때문에 상위 프로세스는 하위 프로세스의 작업 완료 여부를 따로 신경쓰지 않는다. 또한 논블로킹 방식이기 때문에 상위 프로세스는 하위 프로세스에게 일을 맡기고 자신의 작업을 계속 수행할 수도 있다.
