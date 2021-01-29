# Spring Boot properties 값 주입 방법

## 들어가기

Spring Boot를 사용하여 외부의 특정 값들으 주입 받아야할 때가 있다. 예로 API를 사용하기 위한 API key나 token값이 될 수 있다.

이런 값들은 `application.properties`나 `yml`과 같은 파일에 적어두고 사용할 수 있고, .jar 파일을 실행하기 위한 커맨드에 직접 값을 넘겨 주기도 한다.

여기서는 외부 파일(.properties, .yml)에 있는 값들을 소스 코드에 주입하는 방법을 알아보자.

</br >

## properties대신 yml을 사용했을 시 이점

### 1. 가독성이 좋다

- yml을 사용하면 계층구조로 표현하여 가독성이 좋다.
- 불필요한 prefix의 중복 제거가 가능하다.

### properties

~~~properties
spring.datasource.url=jdbc:h2:tcp://localhost/~/testdb
spring.datasource.username: sa
spring.datasource.password:
spring.datasource.driver-class-name: org.h2.Driver
~~~

### yml

```yml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/testdb
    username: sa
    password:
    driver-class-name: org.h2.Driver
```

</br >

### 2. 리스트 표현

- 여러 줄에 쓸 때에는 하이픈(-)으로 시작하는 한 줄에 하나의 요소를 표현한다.
- 한 줄에 모아 쓸 때에는 대괄호([])를 이용하며, 쉼표로 각 요소를 구분한다.

### properties

~~~properties
test.servers[0]=local.example.com
test.servers[1]=dev.example.com
~~~

### yml - 여러 줄 표현

~~~yaml
test:
  servers:
    - local.example.com
    - dev.example.com
~~~

### yml - 한 줄 표현

~~~yaml
test:
  servers: [local.exmaple.com, dev.example.com]
~~~

</br >

### 3. SpringBoot Profile 적용이 용이하다.

- 한 파일 내에서 여러 파일을 사용하는 것처럼 분리가 가능하다.
- `application.yml` 파일 하나로 여러개의 `yml`을 생성한 것과 같이 처리 가능하다.

### properties

- application-{file-name}.properties 형식으로 생성할 수 있다.
  - application-local.properties
  - application-dev.properties

### yml

- profiles를 선언하여 한 yml파일 내에서 구분 가능하다.

~~~yaml
spring:
  profiles:
    active: local #적용할 profile 선택
    
 --- #local 환경
 spring:
   profiles: local
 logging:
   level:
     root: debug

 --- #dev 환경
 spring:
   profiles: dev
 logging:
   level:
     root: info
~~~

</br >

## 외부 파일 값 주입하기

## @ConfigurationProperties

### yml 예제 코드

~~~yml
cafeteria:
  :url: example.com
  :key: mykey
~~~

### Properties 클래스

~~~java
@Getter
@Setter
@ConfigurationProperties(prefix = "cafeteria")
public class CafeteriaProperties {
    private String url;
    private String key;
}
~~~

- 위와 같이 properties클래스를 정의해서 사용한다.
- `@ConfigurationProperties`를 사용할 때 `prefix`를 적어줘야 한다.
- 위 코드를 보면 `@Setter`를 정의해 줬다. `setter`가 없으면 `Caused by: java.lang.IllegalStateException: No setter found for property`가 발생한다.

</br >

## @ConstructorBinding

`@ConfigurationProperties`를 사용한 코드르 보면 `final` 필드로 인스턴스 변수를 생성할 수 없다. 그렇기 때문에 불변성을 유지할 수 없는 문제가 생긴다.

또한 `setter`가 공개되어 있어 악의적으로 중간에 값을 변경할 수 있는 위험이 있다. 이를 해결하기 위한 방법으로 `@ConstructorBinding`를 사용할 수 있다.

`@ConstructorBinding`어노테이션은 `final`필드에 대해 값을 주입해준다. `final`을 명시해 주지 않으면 `setter`를 이용해서 값을 binding하려 하기 때문에 주의하자.

~~~java
@Getter
@RequiredArgsConstructor
@ConstructorBinding
@ConfigurationProperties(prefix = "cafeteria")
public class CafeteriaProperty {
    private final String url;
    private final String key;
}
~~~

</br >

## @EnableConfigurationProperties

위 코드는 다음과 같은 에러 메시지를 보여준다.

~~~
Not registered via @EnableConfigurationProperties, marked as Spring component, or scanned via @ConfigurationPropertiesScan 
~~~

해결 방법으로 `@EnableConfigurationProperties`을 이용해서 Properties 클래스의 클래스 타입을 명시해주면 된다.

~~~java
@Configuration
@EnableConfigurationProperties(FreeCafeteriaProperty.class)
public class PropertiesConfig {
}
~~~

