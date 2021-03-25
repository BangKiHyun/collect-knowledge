# Spring Boot 의존성 주입 방법

Spring Boot에서 의존성 주입 방법으로 3가지가 있습니다. 지금부터 스프링의 의존성 주입 방법에 대해서 알아보겠습니다.

## 1. 생성자 주입(Constructor Injection)

생성자 주입 방법은 Spring framework reference에서 가장 권장하는 방법입니다. 그 이유는 아래에서 설명해 드리겠습니다.

~~~java
@Service
public class SampleService {

    private final SampleRepository sampleRepository;

    @Autowired
    public SampleService(SampleRepository sampleRepository) {
        this.sampleRepository = sampleRepository;
    }
}
~~~

생성자에 `@Autowired`애너테이션을 붙여 의존성을 주입받을 수 있습니다.

하지만 Spring 4.3부터 **단일 생성자이고, 그 생성자의 파라미터가 Bean일 경우 스프링이 `@Autowired`를 사용하지 않아도 생성자를 자동으로 주입**해줍니다.

~~~java
@Service
public class SampleService {

    private final SampleRepository sampleRepository;

    //단일 생성자이기 때문에 @Autowired 생략가능
    public SampleService(SampleRepository sampleRepository) {
        this.sampleRepository = sampleRepository;
    }
}
~~~

</br >

## 2. 필드 주입(Field Injection)

변수 선언부에 `@Autowired` 애너테이션을 붙여주면 됩니다. 편리하기 때문에 가장 많이 접할수 있는 방법입니다.

~~~java
@Service
public class SampleService {
    
    @Autowired
    private SampleRepository sampleRepository;
}
~~~

</br >

## 3. 수정자 주입 (Setter Injection)

Setter 메서드에 @Autowired 애너테이션을 붙여주면 됩니다.

~~~java
@Service
public class SampleService {
    private SampleRepository sampleRepository;
    
    @Autowired
    public void setSampleRepository(SampleRepository sampleRepository){
        this.sampleRepository = sampleRepository;
    }
}
~~~

</br >

## 생성자 주입 방법을 권장하는 이유

### 1. 의존 관계 및 복잡성을 쉽게 알 수 있습니다.

의존성을 주입할 때 한 개의 컴포넌트에서 수 많은 의존성을 갖게될 수 있습니다.

~~~java
@Service
public class SampleService {
    @Autowired
    private SampleRepository1 sampleRepository1;

    @Autowired
    private SampleRepository2 sampleRepository2;
    
    @Autowired
    private SampleRepository3 sampleRepository3;

    @Autowired
    private SampleRepository4 sampleRepository;
    
    //more
}
~~~

위 코드는 `@Autowired` 선언 아래 수 많은 의존성을 추가했습니다. 여기서 생성자 주입 방법을 사용하면 다른 Injection에 비해 코드의 복잡성을 쉽게 알 수 있습니다.

생성자의 파라미터가 많아 짐에 따라 하나의 클래스가 많은 책임을 갖고있다는 걸 알게됩니다. 이는 책임을 분배해야 한다는 신호가 될 수 있습니다.

</br >

### 2. 불변 객체(Immutablility)로 만들 수 있습니다.

생성자 주입 방법에서 해당 필드를 final로 선언할 수 있습니다.

하지만 필드 주입과 수정자 주입 방법은 해당 필드를 final로 선언할 수 없습니다.

~~~java
@Service
public class SalmpleService {
    private final SampleRepository sampleRepository;

    public SalmpleService(SampleRepository sampleRepository) {
        this.sampleRepository = sampleRepository;
    }
}
~~~

생성자 주입은 불변 객체로 만들지 수 있기 때문에 예기치 못한 오류를 사전에 방지할 수 있습니다.

</br >

### 3. 순환 참조를 방지할 수 있습니다.

순환 참조는 1번 클래스가 2번 클래스를 참조하고, 다시 2번 클래스가 1번 클래스를 참조하는 경우 발생합니다.

생성자 주입 방법을 사용하면 순환 참조를 방지할 수 있습니다. 다음 예제를 통해 확인해 보겠습니다.

~~~java
@Service
public class SampleService {

    private final newSampleService newSampleService;

    @Autowired
    public SampleService(newSampleService newSampleService) {
        this.newSampleService = newSampleService;
    }
}
~~~

```java
@Service
public class newSampleService {

    private final SampleService sampleService;

    @Autowired
    public newSampleService(SampleService sampleService) {
        this.sampleService = sampleService;
    }
}
```

위 코드를 실행해보면 **BeanCurrentlyInCreationException**이 발생하며 다음과 같은 오류와 함께 애플리케이션이 구동조차 되지 않습니다. 즉, 순환 참조의 오류를 사전에 알 수 있습니다.

![img](https://blog.kakaocdn.net/dn/wvYRX/btq03uTQVzb/7tOdPZiid0LIb6nR0J7NGK/img.png)



~~~

~~~

