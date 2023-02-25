## 분산락

---
### 단일 서버에서 동시성을 확보하는 방법

- Synchronized 키워드 사용

```java
public synchronized void concurrency(){
    stock.decrease(count) // 동시성 제어가 필요한 작업   
}
```
synchronized 키워드는 프로세스 단위로 동작한다. 
따라서 서버를 다중화시킨 경우에는 synchronized가 모든 서버를 제어하지 못한다. 
이때는 모든 서버가 공통으로 사용할 수 있는 제어 수단이 필요하고 분산락을 사용할 수 있다.

### 분산락 사용 방법

---

Redis를 사용하는 방법과 MySQL의 네임드 락을 사용하는 방법이 있다.


**MySQL의 Named Lock 사용**

```java
// 락 구현
public interface LockRepository extends JpaRepository<Stock, Long> {

    @Query(value = "select get_lock(:key,3000)", nativeQuery = true)  // 2 ~ 5 초 사이가 락 타임아웃으로 적당 , 너무 짧으면 트랜잭션보다 락이 먼저 풀려서 데이터 정합성 깨질 수 있다!
    void getLock(String key);

    @Query(value = "select release_lock(:key)",nativeQuery = true)
    void releaseLock(String key);
}
    
    // 실제 사용 부분
    @Transactional
    public void decrease(Long id, Long quantity)
    {
        try {
            lockRepository.getLock(id.toString());
            stockService.decrease(id,quantity);
        }
        finally {
            lockRepository.releaseLock(id.toString());
        }
    }
```
### Redis를 사용한 분산락 구현

- Lettuce를 사용한 스핀 락 기반 분산락
- Redisson을 사용한 Pub-Sub 기반 분산락

**Lettuce를 사용한 스핀락 구현**

스핀락 : 락을 얻을때까지 루프를 돌며 계속 락 획득 요청을 레디스에게 보내는 방식

레디스의 `setnx` 명령어를 통해 구현할 수 있다.

```java
// 락 로직 등록 부분
@Component
public class RedisRepository {

    private RedisTemplate<String,String> redisTemplate;

    public RedisRepository(RedisTemplate<String, String> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public Boolean lock(Long key)
    {
        return redisTemplate
                .opsForValue()
                .setIfAbsent(generateKey(key),"lock", Duration.ofMillis(3_000));
    }

    private String generateKey(Long key)
    {
        return key.toString();
    }

    public Boolean unLock(Long key)
    {
        return redisTemplate.delete(generateKey(key));
    }
}

    // 실제 락 사용 부분
    public void decrease(Long key, Long quantity) throws InterruptedException {

        while(!repository.lock(key))
        {
            // 스핀 락 방식이기 때문에 부하를 주지 않기 위해서 어느정도 sleep 이후에 재시도를 해야 한다.
            Thread.sleep(100);
        }

        try {
            stockService.decrease(key,quantity);
        }
        finally {
            repository.unLock(key);
        }
    }
```



`setnx`의 문제점
- 락을 기다리는 것과 락을 획득하는것이 원자적으로 연산되지만 **타임아웃 설정 불가능하다**. 
- 락 획득 재시도로 인한 과도한 트래픽을 방지하기 위해 락 획득시 최대 유지 기간이나 최대 허용 횟수 등을 정해줘야 한다.

지속적으로 락 획득 요청을 레디스로 보내기 때문에 레디스에 부담을 줄 수 있는 방식이다. 대체 수단으로 분산락 라이브러리인 `Redisson`을 사용할 수 있다.


### Redisson

Redisson의 특징
- Non-blocking I/o 사용
- lock에 타임아웃 구현되어 있음
- 파라미터에 락 획득 대기 타임아웃과 락 만료 시간이 들어감
- Pub-Sub 구조를 사용해서 레디스에 부담을 주지 않음

Pub-Sub 구조 동작 방식

- 락이 해제될때 채널을 구독하고 있는 클라이언트들에게 알림을 준다.
- 락 해제되었다고 하면 획득 시도 -> 실패하면 대기 -> 그러다가 타임아웃 되면 예외 발생

```java
@Component
public class RedissonLockStockFacade {

    // Redisson 라이브러리 다운로드 시 사용 가능
    private RedissonClient redissonClient;
    
    private StockService stockService;

    public RedissonLockStockFacade(RedissonClient redissonClient, StockService stockService) {
        this.redissonClient = redissonClient;
        this.stockService = stockService;
    }

    public void decrease(Long key, Long quantity)
    {
        // 락 생성
        RLock lock = redissonClient.getLock(key.toString());

        try {
            // 5초 동안 기다리면서 sub로 이벤트가 오기를 기다림, 락 얻으면 1초동안 락 소유할 수 있음
            boolean available = lock.tryLock(5, 1, TimeUnit.SECONDS);
            
            if(!available)
            {
                System.out.println("lock 획득 실패");
                return;
            }
            // 락을 얻은 후 작업
            stockService.decrease(key,quantity);
            
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            // 완료 후에는 락 해제
            lock.unlock();
        }

    }
}
```

참고 레퍼런스

- [분산락 하이퍼커넥트 자료](https://hyperconnect.github.io/2019/11/15/redis-distributed-lock-1.html)
- [우아한 형제들 MySQL 네임드 락 사용](https://techblog.woowahan.com/2631/)