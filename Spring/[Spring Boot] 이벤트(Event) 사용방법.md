# [Spring Boot] 이벤트(Event) 사용방법

## 이벤트(Event)란?

- 이벤트는 '과거에 벌어진 어떤 것'을 뜻한다. 예로, 주문을 취소했다면 '주문을 취소했음 이벤트'가 발생했다고 할 수 있다.
- 이벤트가 발생한다는 것은 상태가 변경됐다는 것을 의미한다. 즉, '주문 취소 이벤트'가 발생한 이유는 주문이 취소 상태로 바뀌었기 때문이다.
- 이벤트가 발생하면 그 이벤트에 반응하여 원하는 동작을 수행하는 기능을 구현하면 된다.

</br >

## 이벤트 필요성

### 강결합 문제

어느 쇼핑몰에서 구매를 취소하면 환불을 처리해야 한다. 이때 주문을 취소하는 로직과 환불을 위한 로직이 모두 섞이게 된다. 이렇게 서로 강한 결합으로 묶여있으면 코드도 복잡해지고, 나중에 유지보수가 힘들어질 수 있다.

### 트랜잭션 처리 문제

환불 기능을 실행하는 과정에서 익셉션이 발생하면 트랜잭션을 롤백해야 할까? 일단 커밋해야 할까?

- 환불에 실패했으므로 주문 취소 트랜잭션을 롤백한다.
- 주문 취소 상태로 변경하고 환불만 나중에 다시 시도한다.

### 성능 문제

만약 주문을 취소할 때 환불 처리 기능이 1분이 걸리면 주문 취소 기능은 1분만큼 대기 시간이 증가한다.

즉, 외부 서비스 성능에 직접적인 영향을 받는 문제가 있다.

</br >

## 이벤트 구성요소

### 이벤트 생성 주체

- 이벤트 생성 주체는 이벤트를 발생시켜 이벤트 디스패처에게 전달한다.
- 주로 도메인 객체가 이벤트 생성 주체가 된다.

### 이벤트 디스패처(dispatcher)

- 이벤트 디스패처는 이벤트 생성 주체와 이벤트 핸들러를 연결해 준다.
- 이벤트 생성 주체는 이벤트를 생성해서 디스패처에 이벤트를 전달한다.
- 이벤트 디스패처의 구현 방식에 따라 이벤트 생성과 처리를 동기나 비동기로 실행하게 된다.

### 이벤트 핸들러(handler)

- 이벤트 핸들러는 생성 주체가 발생한 이벤트를 전달받아 이벤트에 담긴 데이터를 이용해서 원하는 기능을 실행한다.
- 예로, '주문 취소됨 이벤트'를 받는 이벤트 핸들러는 해당 주문의 주문자에게 SMS로 주문 취소 사실을 통지할 수 있다.

</br >

## 이벤트 구현 (V1)

스프링 4.2 이전 버전에서의 이벤트 구현 방법이다.

</br >

### 1. 이벤트 클래스

`ApplicationEvent`를 상속받아서 Event 클래스를 정의하자.

~~~java
public class OrderRefundedEvent extends ApplicationEvent {

    private final Long orderId;

    public OrderRefundedEvent(Object source, Long orderId) {
        super(source);
        this.orderId = orderId;
    }

    public Long getOrderId() {
        return orderId;
    }
}
~~~

- 이벤트는 현재 기준으로 과거에 벌어진 것을 표현하기 때문에 이벤트 이름에 과서 시제를 사용했다.
- **이벤트는 이벤트 핸들러가 작업을 수행하는 데 필요한 최소한의 데이터를 담아줘야 한다.** 그렇지 않으면 핸들러는 필요한 데이터를 읽기 위해 관련 API를 호출하거나 DB에서 데이터를 직접 읽어와야 한다.

</br >

### 2. 이벤트 핸들러

이벤트 핸들러는 `ApplicationListener` 인터페이스를 구현하면 된다.

```java
@Component
public class OrderRefundedHandler implements ApplicationListener<OrderRefundedEvent> {

    private final OrderRefundService refundService;

    public OrderRefundedHandler(OrderRefundService refundService) {
        this.refundService = refundService;
    }

    @Override
    public void onApplicationEvent(OrderRefundedEvent event) {
        refundService.refund(event.getOrderId());
    }
}
```

- 이벤트가 발생했을 때 기능을 수행할 구문은 `onApplicationEvent`메서드에 작성하면 된다.
- 여기서는 주문이 취소되었을 때 환불을 처리해줘야 하기 때문에 `OrderRefundService`에 대한 의존성을 갖는다.

</br >

### 3. 이벤트 생성 주체 (도메인 모델)

```java
@Slf4j
@Getter
public class Order {

    private Long orderId;
    private OrderStatus status;

    public Order(Long orderId) {
        this.orderId = orderId;
    }

    public void cancel(ApplicationEventPublisher publisher) {
        verifyNotYetShipped();
        this.status = OrderStatus.CANCELED;
        try {
            publisher.publishEvent(new OrderRefundedEvent(this, orderId));
        } catch (AlreadyShippedException e) {
            log.error(e.getLocalizedMessage());
        }
    }

    private void verifyNotYetShipped() {
        if (!isNotYetShipped())
            throw new AlreadyShippedException("order is already shipped");
    }

    public boolean isNotYetShipped() {
        return status == OrderStatus.PAYMENT_WAITING || status == OrderStatus.PREPARING;
    }
}
```

- 도메인 모델에서 이벤트를 발생시킨다. `ApplicationEventPublisher`를 사용해서 원하는 이벤트 객체를 생성해 넘겨주면 된다.

</br >

### 4. 주문 취소 서비스

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderCancelService {

    private final OrderRepository orderRepository;
    private final ApplicationEventPublisher publisher;

    public void cancel(Long orderId) {
        final Order order = orderRepository.findById(orderId)
                .orElseThrow(() -> new IllegalArgumentException("not found order"));
        order.cancel(publisher);
    }
}
```

- `OrderCancelService`에서 `cancel()`메서드를 실행한다. 이때 `ApplicationEventPublisher`를 전달하는 방식으로 구현했다.

</br >

### 테스트

```java
@SpringBootTest
class OrderCancelServiceTest {

    @Autowired
    private OrderRefundService orderRefundService;
    @MockBean
    private OrderRepository orderRepository;
    @MockBean
    private OrderCancelService orderCancelService;

    @Test
    @DisplayName("주문에서 이벤트를 발생시켜 환불이 잘 되는지 확인한다.")
    public void cancelTest() throws Exception {
        //given
        long orderId = 7;
        when(orderRepository.findById(orderId)).thenReturn(Optional.of(new Order(orderId)));

        //when
        orderCancelService.cancel(orderId);

        //then
        verify(orderCancelService, times(1)).cancel(orderId);
    }
}
```

![img](https://blog.kakaocdn.net/dn/cdzqSM/btq7ucUkIJQ/twkiVrQaFLk29NKdkkzHSK/img.png)

테스트 코드가 무사히 통과됐다.

</br >

## 이벤트 구현 (V2)

스프링 4.2 부터 `@EventListener`를 사용할 수 있다.

### 1. 이벤트 클래스

V1에서 사용했던 `ApplicationEvent`를 상속받을 필요가 없다.

~~~java
@Getter
public class OrderRefundedEvent {

    private final Long orderId;

    public OrderRefundedEvent(Long orderId) {
        this.orderId = orderId;
    }
}
~~~

</br >

### 2. 이벤트 핸들러

이벤트 핸들러는 `ApplicationListener`를 구현할 필요가 없다.

~~~java
@Component
@RequiredArgsConstructor
public class OrderRefundedHandler {

    private final OrderRefundService refundService;

    @Async
    @EventListener
    public void refund(OrderRefundedEvent event) {
        refundService.refund(event.getOrderId());
    }
}
~~~

- `@EventListener` 어노테이션을 메서드 상단에 선언해주면 이벤트 리스너로 등록이 된다.
- `@Async` 어노테이션을 통해 비동기로 이벤트를 동작하게 할 수 있다. 참고로 `@Async`를 통해 비동기 메서드가 동작할 수 있도록 최상단 클래스에 `@EnableAsync`어노테이션을 선언해줘야 한다.

~~~java
@EnableAsync
@SpringBootApplication
public class SpringLabApplication {
  
    public static void main(String[] args) {
        SpringApplication.run(SpringLabApplication.class, args);
    }
}
~~~

</br >

## 참고

- [DDD START](http://www.yes24.com/Product/Goods/27750871)
- https://brunch.co.kr/@springboot/422#comment

