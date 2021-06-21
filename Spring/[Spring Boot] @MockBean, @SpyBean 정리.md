# [Spring Boot] @MockBean, @SpyBean 정리

Spring Boot를 사용할 때 Junit을 이용해 테스트 코드를 작성하다 보면 보통 여러 레파지토리와 비즈니스 로직이 함께 있어 테스트 환경을 설정하는데 많은 시간을 사용하고 테스트 대상에 집중하는데 어려움을 느낄 수 있습니다.

이러한 문제를 해결하기 위해 **테스트 더블** 이라는 것이 나왔습니다. 테스트 더블은 목적에 따라 비슷하면서도 다른 객체를 사용하는 모든 행위를 말합니다. Java에서는 대표적으로 **Mockito**가 있습니다.

Mockito에는 다양한 어노테이션이 있는데 이중에서 `@MockBean`과 `@SpyBean`에 대해 알아보겠습니다.

</br >

## @MockBean

`@MockBean`은 기존에 사용되던 Bean의 껍데기만 가져오고 내부 구현은 모두 사용자에게 위임하는 형태입니다.

즉, 해당 Bean의 어떤 메서드가 입력되면 어떤 값이 리턴되어야 한다는 내용 모두 필요에 의해 조작이 가능합니다.

</br >

## 코드 예제

### Order.java

~~~java
@Entity
@Getter
@NoArgsConstructor
public class Order {

    @Id
    @GeneratedValue(strategy = IDENTITY)
    @Column(name = "order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "product_id")
    private Product product;

    public Order(User user, Product product) {
        this.user = user;
        this.product = product;
    }
}
~~~

</br >

### Product.java

```java
@Entity
@Getter
@NoArgsConstructor
public class Product {

    @Id
    @GeneratedValue(strategy = IDENTITY)
    @Column(name = "product_id")
    private Long id;

    @ManyToOne(fetch = LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @Column(name = "name")
    private String name;

    @Column(nullable = false)
    private int price;

    public Product(User user, String name, int price) {
        this.user = user;
        this.name = name;
        this.price = price;
    }
}
```

</br >

### User.java

~~~java
@Entity
@Getter
@NoArgsConstructor
public class User {

    @Id
    @GeneratedValue(strategy = IDENTITY)
    @Column(name = "user_id")
    private Long id;

    @Email
    @Column(name = "email", unique = true, nullable = false)
    private String email;

    @Column(name = "password", nullable = false)
    private String password;

    @Column(name = "name", nullable = false)
    private String name;

    @Column(name = "phone_number", nullable = false)
    private String phoneNumber;

    public User(String email, String password, String name, String phoneNumber) {
        this.email = email;
        this.password = password;
        this.name = name;
        this.phoneNumber = phoneNumber;
    }
}
~~~

</br >

### OrderService.java

~~~java
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository orderRepository;
    private final ProductRepository productRepository;
    private final UserRepository userRepository;

    @Transactional
    public Long order(Long productId, Long userId) {
        final Optional<Product> product = productRepository.findById(productId);

        final Optional<User> user = userRepository.findById(userId);

        final Order order = new Order(user.get(), product.get());
        orderRepository.save(order);

        return order.getId();
    }
}
~~~

상황은 다음과 같습니다.

1. 주문하는 회원을 조회합니다.
2. 주문하는 상품을 조회합니다.
3. 주문이 성공합니다.

</br >

### 테스트 코드

~~~java
@SpringBootTest
class OrderServiceTest {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private ProductRepository productRepository;
    @Autowired
    private OrderRepository orderRepository;
    @Autowired
    private OrderService orderService;

    @Test
    @DisplayName("상품을 주문한다.")
    void orderTest() {

        //given
        final User user = new User("test@test.com", "1234", "bang", "010-1234-5678");
        userRepository.save(user);

        final Product product = new Product(user, "good product", 12000);
        productRepository.save(product);

        //when
        final Long orderId = orderService.order(product.getId(), user.getId());

        //then
        assertThat(orderId).isEqualTo(1L);
    }
}
~~~

### 테스트 조건

- 해당 코드를 실행시키기 위해서 DB 환경 설정이 제대로 되어있어야 합니다.
- 데이터베이스에 `User`와 `Product`의 컬럼값이 하나도 없어야 합니다.
- `User`와 `Product`의 인스턴스의 필드값을 채운 후, DB에 저장해야 합니다.

실제 테스트 하려는 로직은 간단하지만, 이를 실행시키기 위해 설정해야 하는 작업이 너무 많습니다. `@MockBean`을 이용해 해결해보겠습니다.

</br >

### MockBean을 사용한 테스트 코드

~~~java
@SpringBootTest
public class OrderServiceTest2 {

    @Autowired
    private OrderService orderService;

    @MockBean(name = "orderRepository")
    private OrderRepository orderRepository;

    @MockBean(name = "productRepository")
    private ProductRepository productRepository;

    @MockBean(name = "userRepository")
    private UserRepository userRepository;

    @Test
    @DisplayName("상품을 주문한다.")
    void orderTest() {
      
        //given
        User user = new User();
        given(userRepository.findById(1L))
                .willReturn(Optional.of(user)); // userRepository.findById(1L)을 호출할 때 user를 리턴

        Product product = new Product();
        given(productRepository.findById(1L))
                .willReturn(Optional.of(product)); // productRepository.findById(1L)을 호출할 때 user를 리턴

        Order order = Order.builder()
                .id(1L)
                .build();
        given(orderRepository.save(order))
                .willReturn(order);

        //when
        orderService.order(1L, 1L);

        //then
        assertThat(order.getId()).isEqualTo(1L);
    }
}
~~~

위와 같이 테스트 코드를 실행하면 `UserRepository` `ProductRepository`, `OrderRepository`는 모두 테스트 코드에서 선언한 **Mock Bean**이 주입되어 실행됩니다. 그렇기 때문에 메서드 호출시 `given`에 선언된 값이 반환됩니다.

테스트 코드는 다음과 같이 잘 동작합니다.

![image](https://user-images.githubusercontent.com/43977617/122711704-923b7280-d29d-11eb-9b45-9b8e0ad89886.png)

### 정리

- 테스트를 실행하기 위해 DB 설정이 제대로 되어있는지, DB에 데이터가 제대로 들어갔는지 신경쓰지 않아도 됩니다.
- `User`와 `Product`의 인스턴스의 필드값을 채울 필요가 없습니다.
- 즉, 테스트 코드에 좀 더 집중할 수 있습니다.

</br >

## @SpyBean

`@MockBean`은 `given`에서 사용한 코드 외에는 전부 사용할 수 없지만,  `@SpyBean`은 `given`에서 선언한 코드 외에는 전부 실제 객체인 것을 사용합니다.

즉, `@SpyBean`으로 등록한 객체가 수행하는 기능이 여러개가 있는데, 그 중 몇가지만 조작하고 나머지 기능은 기존 기능을 유지하고 싶을 때 사용하면 됩니다.

</br >

## 코드 예제

상품을 주문할 때 모바일과 이메일로 알람을 보내주는 기능이 있다고 가정해보겠습니다.

### NotificationClientService.java

```java
@Service
public class NotificationClientService {

    public void notifyToEmail(){
        System.out.println("i'm real notification client email service!");
    }

    public void notifyToMobile() {
        System.out.println("i'm real notification client mobile service!");
    }
}
```

</br >

### OrderService.java

~~~java
@Slf4j
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository orderRepository;
    private final ProductRepository productRepository;
    private final UserRepository userRepository;
    private final NotificationClientService notificationClientService;

    @Transactional
    public Long order(Long productId, Long userId) {
        final Optional<Product> product = productRepository.findById(productId);

        final Optional<User> user = userRepository.findById(userId);

        final Order order = new Order(user.get(), product.get());
        orderRepository.save(order);

        notificationClientService.notifyToEmail(); // email 알림
        notificationClientService.notifyToMobile(); // mobile 알림

        return order.getId();
    }
}
~~~

</br >

### SpyBean 테스트 코드

~~~java
@SpringBootTest
public class OrderServiceTest2 {

    @Autowired
    private OrderService orderService;

    @MockBean(name = "orderRepository")
    private OrderRepository orderRepository;

    @MockBean(name = "productRepository")
    private ProductRepository productRepository;

    @MockBean(name = "userRepository")
    private UserRepository userRepository;

    @SpyBean
    private NotificationClientService notificationClientService; // SpyBean으로 등록

    @Test
    @DisplayName("상품을 주문한다.")
    void orderTest() {
        //given
        User user = new User();
        given(userRepository.findById(1L))
                .willReturn(Optional.of(user));

        Product product = new Product();
        given(productRepository.findById(1L))
                .willReturn(Optional.of(product));

        Order order = Order.builder()
                .id(1L)
                .build();
        given(orderRepository.save(order))
                .willReturn(order);

        doAnswer(invocation -> {
            System.out.println("i'm spy notification client mobile service!");
            return null;
        }).when(notificationClientService).notifyToMobile(); // notifyToMoblie() 메서드만 재정의

        //when
        orderService.order(1L, 1L);

        //then
        assertThat(order.getId()).isEqualTo(1L);
    }
}
~~~

위 코드는 `NotificationClientService`를 `@SpyBean`으로 등록해 줬습니다. 그리고 `notifyToMoblie()`만 조작을 해줬습니다.

결과는 다음과 같습니다.

![image](https://user-images.githubusercontent.com/43977617/122715813-6d96c900-d2a4-11eb-8659-687005d91594.png)

결과를 보면 `notifyToEmail()`메서드는 실제 객체를 사용한 것을 볼 수 있고, `notifyToMobile()` 메서드는 테스트 코드에서 재정의한 기능을 사용합니다.

</br >

### @SpyBean 사용시 유의할 점

**`@SpyBean`은 실제 구현된 객체를 감싸는 프록시 객체 형태이기 때문에 스프링 컨텍스트에 실제 구현체가 등록되어 있어야 합니다.** 만약 실제 구현체가 등록되어 있지않으면 에러가 발생합니다.

예를 들어, `@SpyBean`으로 등록한 객체가 Interface일 경우 해당 Interface를 구현하는 실제 구현체가 꼭 스프링 컨텍스트에 등록되어 있어야 합니다. 그렇지 않으면 에러가 발생합니다.

하지만 `@MockBean`으로 변경하게 되면 기존 스프링 컨텍스트에 등록된 구현체를 사용하는것이 아닌 껍데기만 가진 Mock객체를 스프링 컨텍스트에 등록하는 것이기 때문에 에러가 발생하지 않습니다.

