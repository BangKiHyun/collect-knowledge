# 빈 주입 대상 설정하기

Spring은 기본적으로 빈 주입을 설정을 자동으로 해준다. 하지만 동일한 타입을 가진 빈 객체가 두개 이상이라면 어떤 빈을 주입해야 할 지 알 수 없기 때문에 *NoUniqueBeanDefinitionException* 발생시킨다.

이 문제를 해결하기 위해 **@Qualifier** 어노테이션을 사용하면 된다.

</br >

## @Qualifier

`@Qualifier` 어노테이션을 사용할 의존 객체를 선택할 수 있도록 도와준다.

## 코드 예제

### MainRestTemplateConfig.class

~~~java
@Configuration
public class MainRestTemplateConfig {

    private static final int READ_TIME = 3000;
    private static final int CONNECT_TIME = 3000;

    @Bean(name = "mainRestTemplate") // 빈 이름 설정
    public RestTemplate restTemplate() {
        RestTemplateBuilder builder = new RestTemplateBuilder();
        return builder.setReadTimeout(Duration.ofMillis(READ_TIME))
                .setConnectTimeout(Duration.ofMillis(CONNECT_TIME))
                .build();
    }
}
~~~

</br >

### CandidateRestTemplateConfig.class

~~~java
@Configuration
public class CandidateRestTemplateConfig {

    private static final int READ_TIME = 10000;
    private static final int CONNECT_TIME = 10000;

    @Bean(name = "candidateRestTemplate") // 빈 이름 설정
    public RestTemplate restTemplate() {
        RestTemplateBuilder builder = new RestTemplateBuilder();
        return builder.setReadTimeout(Duration.ofMillis(READ_TIME))
                .setConnectTimeout(Duration.ofMillis(CONNECT_TIME))
                .build();
    }
}

~~~

</br >

## @Configuration

위 코드는 `@Configuration` 어노테이션을 사용해서 Bean을 생성했다. `@Configuration`에 대한 설명은 다음과 같다.

- `@Configuration` 어노테이션은 해당 클래스가 1개 이상의 Bean을 생성하고 있음을 명시한다.
- 그렇기 때문에 `@Bean` 어노테이션을 사용하는 클래스의 경우 반드시 `@Configuration`을 붙여줘야 한다.
- `@Bean`으로 정의된 메서드들은 Spring Container가 런타임에 Bean 정의 및 서비스 요청을 생성하기 위해 처리할 수 있음을 선언한다.

### 언제 사용하나?

- 개발자가 직접 제어가 불가능한 라이브러를 활용할 때
- 초기에 설정을 하기 위해 활용할 때

</br >

## 정의한 RestTemplate 사용

```java
@Service
public class RestTemplateService {

    private RestTemplate restTemplate;

    public RestTemplateService(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    public void call(){
        // doSomething
    }
}
```

위 서비스 코드는 다음과 같은 compile error를 보여준다.

~~~
Could not autowire. There is more than one bean of 'RestTemplate' type.
Beans: candidateRestTemplate (CandidateRestTemplateConfig.java)
mainRestTemplate (MainRestTemplateConfig.java)
~~~

</br >

문제 해결책으로 `@Qualifier` 어노테이션으로 어떤 Bean을 사용할지 명시해주면 된다.

~~~java
@Service
public class RestTemplateService {

    private RestTemplate restTemplate;

    public RestTemplateService(@Qualifier("mainRestTemplate") RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    public void call(){
        // doSomething
    }
}
~~~

</br >

## @Primary

`@Primary`어노테이션은 `@Qualifier`와 마찬가지로 종속성 주입과 관련된 모호성이 있을 때 주입할 빈을 결정하는데 사용할 수 있다.

`@Primary`어노테이션은 **동일한 유형의 여러 Bean이 있는 경우 기본값을 정의**한다.

~~~java
@Configuration
public class CandidateRestTemplateConfig {

    private static final int READ_TIME = 10000;
    private static final int CONNECT_TIME = 10000;

    @Primary
    @Bean(name = "candidateRestTemplate")
    public RestTemplate restTemplate() {
        RestTemplateBuilder builder = new RestTemplateBuilder();
        return builder.setReadTimeout(Duration.ofMillis(READ_TIME))
                .setConnectTimeout(Duration.ofMillis(CONNECT_TIME))
                .build();
    }
}
~~~

