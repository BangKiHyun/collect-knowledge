# [Java] 비동기 처리를 위한 CompletableFuture

최근에 비동기 처리에 대한 관심이 생겼다. Java를 활용한 비동기 처리에 대해 한번 공부해보고자 한다.

</br >

## Sync vs Async

`CompletableFuture`를 공부하기에 앞서 Sync(동기)와 Async(비동기)에 대해 먼저 알아보자. 

이번 포스팅에서 사용할 시나리오는 다음과 같다.

### 시나리오

- 카페에 있는 메뉴의 가격을 조회한다.
- 예를 들어 "iceAmericano"라는 이름의 메뉴를 조회하면 4100원 이라고 응답을 해주는 기능이다.
- 클래스에서는 가격을 조회하는 메서드를 제공한다.
- 클라이언트는 해당 메서드를 호출할 때 커피의 이름을 파라미터로 넘겨주면 4100원이라는 데이터를 리턴받는다.
  - 여기서 클라이언트는 메서드를 호출하는 곳이다.

### Sync

클라이언트에서 "iceAmericano"라는 메뉴를 조회하기 위해서, 클래스의 `getPrice()` 메서드를 실행한다.

메서드를 제공하는 곳에서 기능을 수행한 후 결과값이 결정되면 그때 값을 반환한다. 결과값이 반환될 때까지 다른 작업은 수행할 수 없다.

### Async

비동기 방식은 결과값이 결정되기 전에 일단 반환한다. "iceAmericano"라는 커피의 가격이 4100원이라는 정보를 찾기 전에 일단 임시값을 넘긴다. 그렇기 때문에 결과값을 얻기 전까지 다른 작업 수행이 가능하다.

앞서 말했듯이 메서드를 호출하는 곳, 즉 클라이언트에서는 최종 결과를 받기 전에 메서드로부터 임시로 반환을 받는다. 그렇다면 클라이언트는 최종 결과를 어떻게 알 수 있을까? 두 가지 방법이 있다. Blocking과 Non-Blocking이다.

</br >

## Blocking vs Non-Blocking

Blocking과 Non-Blocking은 메서드를 호출하는 곳, 즉 클라이언트에서의 입장에서의 개념이다.

클라이언트에서 데이터를 조회하는 두가지 방법에 대해 생각해보자. 일단, 위에서 설명했듯이 Async 메서드는 결과를 완성하기 전에 일단 반환한다. 그래서 클라이언트는 결과가 완성되었을 때쯤 다시 메서드를 조회해야 한다.

이 방법이 첫 번째 방법이다. 메서드를 호출한 이후 어느정도 시간이 지난 후 다시 결과를 조회하는 방법이다.

`getPriceAsync()` 메서드를 호출한 이후에 클라이언트는 다른 작업을 수행할 수 있다. 즉, 최종 결과를 조회할 때까지 차단 되지 않고 다른 작업을 할 수 있다. 하지만 다른 작업을 수행하다가, "iceAmericano"라는 가격이 필요한 시점에서 `getPriceAsync()`의 결과를 알고 싶을 때 다시 데이터를 조회해야 한다.

`get()` 이라는 메서드를 사용했다고 가정하자. 이 경우 get() 메서드를 통해서 최종 결과를 전달 받기 전까지 기다려야 한다. 서비스 제공 메서드는 비동기로 구현을 했지만, 클라이언트 입장에서는 항상 Non-Blocking은 아니다. 데이터를 조회하는 순간 다시 Blocking으로 동작한다.

물론 아예 Non-Blocking으로 만들 수 있는 방법이 있다. 콜백 함수를 구현하면 된다. 이 방법은 조금 이따가 알아보자.

</br >

## CompletableFuture

이제 `CompletableFuture` 에 대해서 알아보자.

### 예제 코드

`CompletableFuture`를 알아보기에 앞서 예제 코드를 준비했다. 예제 코드는 SpringBoot와 Java를 활용해서 작성하였다.

일단 카페의 메뉴를 저장한 `Repository`를 정의했다.

```java
@Repository
public class CafeRepository {

    private Map<String, Menu> menuMap = new HashMap<>();

    @PostConstruct
    public void init() {
        menuMap.put("iceAmericano", new Coffee("iceAmericano", 4100));
        menuMap.put("latte", new Coffee("latte", 4500));
        menuMap.put("cheeseCake", new Cake("cheeseCake", 4500));
    }

    public int getPriceByName(String name) {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return menuMap.get(name).getPrice();
    }
}
```

`getPriceByName()` 메서드는 메뉴 이름을 파라미터로 받아서 가격을 알려주는 메서드다. 해당 메서드에 1초의 지연 시간을 설정하였다. 즉, 메뉴의 가격을 조회할 때 최소 1초가 걸릴 것이다.

</br >

다음으로 비즈니스 로직을 포함한 `Service`를 정의했다.

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class CafeServiceImplement {

    private final CafeRepository cafeRepository;

    public int getPrice(String name) {
        log.info("동기 호출 방식으로 가격 조회 시작");
        return cafeRepository.getPriceByName(name);
    }
}
```

</br >

### Sync(동기) 메서드

먼저 Sync 방식 부터 테스트 코드를 통해 검증해보자. 테스트 코드는 다음과 같다.

~~~java
@Slf4j
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = {
        CafeServiceImplement.class,
        CafeRepository.class})
public class CafeTest {

    @Autowired
    private CafeServiceImplement cafeServiceImplement;

    @Test
    public void 가격_조회_동기_블로킹_호출_테스트() throws Exception {
        //given
        int expectedPrice = 4100;

        //when
        final int actualPrice = cafeServiceImplement.getPrice("iceAmericano");
        log.info("최종 가격 전달 받음");

        //then
        assertThat(expectedPrice).isEqualTo(actualPrice);
    }
}

~~~

![image](https://user-images.githubusercontent.com/43977617/134625480-64dfd728-f630-4c78-b0ee-81cbb2e3d9cc.png)

약 1초라는 시간이 걸린걸 확인할 수 있다. 만약, 두번 수행한다면 동기호출이기 때문에 2초정도 소요가 될것이다. 확인해보자.

```java
@Test
public void 가격_조회_동기_블로킹_호출_테스트() throws Exception {
    //given
    int expectedPrice = 4100;

    //when
    final int actualPrice = cafeServiceImplement.getPrice("iceAmericano");
    cafeServiceImplement.getPrice("iceAmericano");
    log.info("최종 가격 전달 받음");

    //then
    assertThat(expectedPrice).isEqualTo(actualPrice);
}
```

![image](https://user-images.githubusercontent.com/43977617/134625830-7068b55a-0515-4460-8f36-2531cdd7b459.png)

에상대로 2초정도가 소요된 걸 확인할 수 있다.

</br >

### Async(비동기), NonBlocking + Blocking

이번에는 Async 메서드를 알아보자.

~~~java
@Slf4j
@Service
@RequiredArgsConstructor
public class CafeServiceImplement {

    private final CafeRepository cafeRepository;
    Executor executor = Executors.newFixedThreadPool(10);

    // blocking + nonblocking
    public CompletableFuture<Integer> getPriceAsync(String name) {
        log.info("비동기 호출 방식으로 가격 조회 시작");
        CompletableFuture<Integer> future = new CompletableFuture<>();

        new Thread(() -> {
            log.info("새로운 쓰레드로 작업 시작");
            Integer price = cafeRepository.getPriceByName(name);
            future.complete(price);
        }).start();

        return future;
    }
}
~~~

코드를 보면 새로운 쓰레드를 생성해서 `Repository`를 통해 데이터를 조회한다.최종 연산이 끝나지 않아도 일단 `future`를 리턴한다.

이제 테스트 코드를통해 검증해보자. `getPriceAsync()`메서드를 두번 호출해 보겠다.

```java
@Slf4j
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = {
        CafeServiceImplement.class,
        CafeRepository.class})
public class CafeTest {

    @Autowired
    private CafeServiceImplement cafeServiceImplement;

    @Test
    public void 가격_조회_비동기_블로킹_호출_테스트() throws Exception {
        //given
        int expectedPrice = 4100;

        //when
        final CompletableFuture<Integer> future = cafeServiceImplement.getPriceAsync("iceAmericano");
        log.info("최종 데이터를 전달 받지는 않았지만  다른 작업 수행 가능");
        cafeServiceImplement.getPriceAsync("iceAmericano"); // 한번 더 호출
        int actualPrice = future.join();
        log.info("최종 가격 전달 받음");

        //then
        assertThat(expectedPrice).isEqualTo(actualPrice);
    }
}
```

![image](https://user-images.githubusercontent.com/43977617/134626810-a66a49c5-8db0-4ab8-b43f-bb7147253d86.png)

결과를 보면 약 1초정도 걸린걸 확인할 수 있다. 즉, `getPriceByNae()`에 1초의 지연시간을 주었지만, 해당 데이터를 기다리지 않고 다른 작업을 병행했다.

위 코드에서 `CompletableFuture`의 `join` 또는 `get`메서드를 사용해서 최종 데이터를 조회해야 한다. `join`이나 `get`을 수행하는 시점에서 데이터를 조회할 때까지 Blocking된다. 그전에는 Non-Blocking 형태로 다른 작업을 수행할 수 있다.

</br >

### Async(비동기) 메서드 리팩토링

`getPriceAsync()` 메서드를 좀 더 깔끔하게 수정할 수 있다. `CompletableFuture`에서 제공하는 `sypplyAsync` 또는 `runAsync`를 사용하면된다.

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
    return asyncSupplyStage(asyncPool, supplier);
}
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier,
                                                   Executor executor) {
    return asyncSupplyStage(screenExecutor(executor), supplier);
}

public static CompletableFuture<Void> runAsync(Runnable runnable) {
    return asyncRunStage(asyncPool, runnable);
}

public static CompletableFuture<Void> runAsync(Runnable runnable,
                                               Executor executor) {
    return asyncRunStage(screenExecutor(executor), runnable);
}
```

`Supplier`는 파라미터는 없지만 반환값이 있는 함수형 인터페이스이고, `Runnable`는 파라미터, 반환 모두 없는 함수형 인터페이스다. `supplyAsync`를 사용해서 리팩토링을 해보자.

```java
public CompletableFuture<Integer> getPriceAsync(String name) {
    log.info("비동기 호출 방식으로 가격 조회 시작");
    return CompletableFuture.supplyAsync(() -> {
                log.info("Supply Async 실행");
                return cafeRepository.getPriceByName(name);
            },
            executor);
}
```

```java
@Test
public void 가격_조회_비동기_블로킹_호출_테스트() throws Exception {
    //given
    int expectedPrice = 4100;

    //when
    final CompletableFuture<Integer> future = cafeServiceImplement.getPriceAsync("iceAmericano");
    cafeServiceImplement.getPriceAsync("iceAmericano");
    log.info("최종 데이터를 전달 받지는 않았지만 다른 작업 수행 가능");
    int actualPrice = future.join();
    log.info("최종 가격 전달 받음");

    //then
    assertThat(expectedPrice).isEqualTo(actualPrice);
}
```

![image](https://user-images.githubusercontent.com/43977617/134628665-551abfcd-6e96-4c67-862d-430de46a3a06.png)

테스트 코드 또한 올바르게 동작하는 걸 확인할 수 있었다.

</br >

### Async(비동기): Non-Blocking 구현

Non-Blocking, Blocking은 메서드를 사용하는 클라이언트에서 결정할 수 있다.

위 코드는 `get` 또는 `join` 메서드를 사용할 때 Blocking현상이 발생한다. Non-Blocking으로 구현하기 위해서는 콜백 함수를 구현해야 하는데, `CompletableFuture`는 `thenAccept`와 `thenApply`를 제공한다,

`thenAccept`는 return값이 없고, `thenApply`는 데이터를 포함하는 `CompleteableFuture`를 반환한다.

`thenAccept`를 사용해서 콜백 함수를 선언해 보겠다.

```java
@Test
public void 가격_조회_비동기_논블로킹_호출_반환없음_테스트() throws Exception {
    //when
    final CompletableFuture<Void> future = cafeServiceImplement
            .getPriceAsync("iceAmericano")
            .thenAccept(p -> {
                log.info("콜백, 가격: " + p + "원");
                assertThat(expectedPrice).isEqualTo(p);
            });
    log.info("최종 데이터를 전달 받지는 않았지만 다른 작업 수행 가능, Non-Blocking");

    //then
    assertThat(future.join()).isNull();
}
```

위 코드에서는 get이나 join을 이용해서 데이터를 조회할 필요가 없다. 왜냐하면 최종 연산이 되면 콜백 함수를 실행해주기 때문이다. 단, 해당 코드는 테스트 코드이기 때문에 제일 하단에 future.join을 실행해서 Blocking코드를 추가했다.

위 코드를 추가한 이유는 해당 코드가 없으면 thenAccept 로직이 수행하기 전에 테스트가 통과해버리기 때문이다. 왜냐하면, 테스트 코드는 Main 쓰레드에서 동작하고, thenAccept 콜백 메서드가 수행하기도 전에 Main 쓰레드가 종료되기 때문이다. 즉, Main 쓰레드를 종료시키지 않기 위해 임의로 작성한 코드다.

</br >

이번에는  `thenApply`를 사용해서 콜백함수를 선언해 보겠다.

```java
@Test
public void 가격_조회_비동기_논블로킹_호출_반환_테스트() throws Exception {
    //given
    int expectedPrice = 4100;

    //when
    final CompletableFuture<Integer> future = cafeServiceImplement
            .getPriceAsync("iceAmericano")
            .thenApply(p -> {
                log.info("같은 쓰레드로 동작");
                return p;
            });
    log.info("최종 데이터를 전달 받지는 않았지만 다른 작업 수행 가능, Non-Blocking");

    //then
    assertThat(future.join()).isEqualTo(expectedPrice);
}
```

위 코드는 `thenAccept`와 다르게 값을 반환받을 수 있다. 값을 반환받고 싶을때는 `thenApply`를 사용해서 콜백함수를 선언하자.

