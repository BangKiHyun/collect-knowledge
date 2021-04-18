# [Spring Boot] RestTemplate Connection Pool

오늘은 Spring Boot에서 RestTemplate Connection Pool을 설정하는 방법에 대해 알아보겠습니다.

RestTemplate은 기본적으로 Connection Pool을 직접적으로 지원하지 않기 때문에 매번 RestTemplate를 호출할 때마다, 로컬에서 임시 TCP 소켓을 개방하여 사용합니다.

이렇게 사용된 TCP 소켓은 TIME_WAIT 상태가 되는데, 요청량이 엄청 나게 많아진다면 이러한 소켓들은 재사용 될 수 없기 때문에 응답이 지연될 수밖에 없습니다.

하지만 RestTemplate의 내부 구성에 의해 Connection Pool을 설정할 수 있습니다. RestTemplate에서 내부적으로 사용되는 `HttpClient`를 이용하면 됩니다.

### RestTemplate.class

~~~java
	/**
	 * Create a new instance of the {@link RestTemplate} based on the given {@link ClientHttpRequestFactory}.
	 * @param requestFactory the HTTP request factory to use
	 * @see org.springframework.http.client.SimpleClientHttpRequestFactory
	 * @see org.springframework.http.client.HttpComponentsClientHttpRequestFactory
	 */
	public RestTemplate(ClientHttpRequestFactory requestFactory) {
		this();
		setRequestFactory(requestFactory);
	}
~~~

RestTemplate는 기본 생성자 외에 `ClientHttpRequestFactory`인터페이스를 받는 생성자가 있습니다. 따로 인자를 보내주지 않으면 기본적으로 `SimpleClientHttpRequestFactory`을 사용합니다.

`SimpleClientHttpRequestFactory`를 대신 `HttpComponentsClientHttpRequestFactory`를 파라미터로 넘겨주면 어떻게 될까요? `HttpComponentsClientHttpRequestFactory`를 한번 살펴보겠습니다.

</br >

### HttpComponentsClientHttpRequestFactory.Class

```java
/**
 * Create a new instance of the {@code HttpComponentsClientHttpRequestFactory}
 * with a default {@link HttpClient} based on system properties.
 */
public HttpComponentsClientHttpRequestFactory() {
   this.httpClient = HttpClients.createSystem();
}

/**
 * Create a new instance of the {@code HttpComponentsClientHttpRequestFactory}
 * with the given {@link HttpClient} instance.
 * @param httpClient the HttpClient instance to use for this request factory
 */
public HttpComponentsClientHttpRequestFactory(HttpClient httpClient) {
   this.httpClient = httpClient;
}
```

`HttpComponentsClientHttpRequestFactory`클래스를 보면 기본 생성자 외에 `HttpClient`를 인자로 받는 생성자가 있습니다. 

바로 이 `HttpClient`에서  Connection Pool을 설정할 수 있습니다. 이제 예제 코드를 통해 RestTemplate의 Connection Pool을 설정하는 방법을 알아보겠습니다.

</br >

우선 `HttpClientBuilder`를 사용하기 위해 다음 dependecy를 추가해줘야 합니다.

~~~java
compile group: 'org.apache.httpcomponents', name: 'httpclient', version: '4.5.6'
~~~

그리고 `HttpClientBuilder`의 `create()` 메서드를 호출한 다음 MaxConnTotal과 MaxConnPerRoute를 설정해 주면 됩니다.

```java
@Configuration
public class RestTemplateConfiguration {

    @Bean
    public RestTemplate restTemplate() {
        final HttpClient httpClient = HttpClientBuilder.create()
                .setMaxConnTotal(100) // 최대 커넥션 수
                .setMaxConnPerRoute(50) // IP포트 1쌍당 동시 수행할 연결 수
                .build();

        final HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(httpClient);
        requestFactory.setConnectTimeout(3000); // 읽기시간 초과
        requestFactory.setReadTimeout(3000); // 연결시간 초과

        return new RestTemplate(requestFactory);
    }
}
```

`MaxConnTotal`로 최대 커넥션 수를 제한하고, `MaxConnPerRoute`로 IP포트 1쌍당 동시 수행할 연결 수를 제한해주면 됩니다.

