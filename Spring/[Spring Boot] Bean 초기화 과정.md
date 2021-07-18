# [Spring Boot] Bean 초기화 과정

## Bean이란?

Bean이란 Spring IoC 컨테이너가 관리하는 자바 객체이다. 이때 Bean의 Scope를 지정할 수 있고, Singleton과 Prototype으로 지정할 수 있다. proptotype은 객체 호출시 매번 새롭게 생성하게 된다.

그렇다면 Bean을 어떻게 만들고 Spring IoC컨테이너에 등록할 수 있을까? 지금부터 한번 알아보도록 하자.

</br >

## Bean 만드는 방법

개발자가 Bean을 만드는 방법은 크게 두 가지가 있다.

`@Component`어노테이션을 이용하는 방법과 `@Bean`어노테이션을 이용하는 방법이다.

</br >

### @Component

`@Component`어노테이션은 클래스에 명시해 주면 된다.

```java
@Component
public class ComponentClazz {
    private final String name = "component class";
}

```

</br >

### @Bean

`@Bean`어노테이션은 `@Configuration`이 등록된 클래스 내부 메서드에 명시해 주면 된다.

참고로 `@Configuration`은 해당 클래스는 하나 이상의  `@Bean`을 등록하고 있음을 명시하고, 해당 Bean을 정의하고 생성할 수 있도록  Spring Container에 의해 처리될 수 있음을 나타낸다.

~~~java
public class BeanClazz {
    private final String name = "bean class";
}

~~~

```java
@Configuration
public class BeanConfigurationClazz {

    @Bean
    public BeanClazz beanClazz() {
        return new BeanClazz();
    }
}
```

</br >

## Bean 등록 방법

Bean이 등록되는 방법으로 두 가지가 있다.

Component Scan 방법과 Auto Configuration 방법이다.

</br >

### Component Scan

Component Scan 방식은 **`@Component`어노테이션이 붙은 클래스를 Bean으로 등록하는 방식이다.** 그리고 `@Component` 어노테이션이 붙은 클래스 안에 `@Bean`어노테이션으로 정의한 메서드의 반환값들도 Bean으로 등록한다. 대신 해당 Bean은 'lite mode'로 사용된다.

![image](https://user-images.githubusercontent.com/43977617/126043381-606336c4-a58d-4037-b6ff-b740eaeaada9.png)

이렇게 `@Component`어노테이션과  `@Bean`어노테이션을 Bean으로 등록하고 사용할 수 있는 이유는 `@ComponentScan`이 동작하기 때문이다.

`@ComponentScan`어노테이션을 따로 설정해 주지 않았는데도 불구하고 왜 Component Scan이 동작할까? 그 이유는 바로 `@SpringBootApplication`어노테이션에 `@ComponentScan` 어노테이션이 있기 때문이다.

![image](https://user-images.githubusercontent.com/43977617/126043578-5df3fe44-9d8d-4f38-87a9-672a8ce369ef.png)

`@ComponentScan`어노테이션은 `value` 또는 `basepackage`로 Component Scan 시작 지점을 명시할 수 있다.

시작 지점으로 지정된 패키지부터 하위 패키지에 있는 `@Component`, `@Bean`어노테이션을 찾아 Bean으로 등록한다. 만약 `value`나 `basepackage`를 명시하지 않는다면 `@ComponentScan`이 작성된 패키지의 하위 패키지에 있는 모든 `@Component`, `@Bean`어노테이션을 찾아 Bean으로 등록한다.

</br >

### Auto Configuration

Auto Configuration 방식은 **Spring Boot가 제공하는 클래스를 Bean으로 등록하는 방식이다.**

`@EnableAutoConfiguration`어노테이션 통해 Spring Boot가 제공하는 클래스를 Bean으로 사용할 수 있다.

![image](https://user-images.githubusercontent.com/43977617/126043861-caff5692-7bc2-470b-abcd-7177577315d3.png)

Spring Boot가 제공하는 클래스는 `spring-boot-autoconfigure-[version].jar/META-INF/spring.factories` 내부에 명시되어있다.

