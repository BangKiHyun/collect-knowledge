# RestTemplate

## RestTemplate?

RestTemplate은 Spring에서 제공하는 http 통신에 유용하게 쓸 수 있는 템플릿입니다.

HTTP 서버와의 통신을 단순화하고 RESTful 원칙을 지키고, jdbcTemplate처럼 기계적이고 반복적인 코드를 깔끔하게 정리해줍니다.

RestTemplate은 `org.springframework.http.client` 패키지에 있으며, HttpClient를 추상화해서 HttpEntity의 json, xml 등 제공해줍니다.

</br >

## RestTemplate 사용 방법

### 1. Bean 설정하기

RestTemplate을 사용하기 위해 일단 Bean 설정을 해줘야 합니다.

매번 RestTemplate를 new 키워드로 생성하는 것은 번거롭고 안전하지 않기 때문에 Spring에서 제공하는 DI를 활용하는 것이 좋습니다.

~~~java
@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate() {
        RestTemplateBuilder builder = new RestTemplateBuilder();
        return builder.setReadTimeout(Duration.ofMillis(3000))
                .setConnectTimeout(Duration.ofMillis(3000))
                .build();
    }
}
~~~

위 코드는 RestTemplateBuilder로 RestTemplate Bean 설정을 해줬습니다.

이 예제에서는 Timeout 설정만 했지만 그 외 Connection Pool과 MessageConverter 등 여러가지 설정을 할 수 있습니다. Connection Pool 설정방법에 대해서는 다른 포스팅에서 다루도록 하겠습니다.

</br >

### 2. API 호출

RestTemplate는 API 호출 기능을 하는 다양한 메서드를 제공합니다.

### Method 종류

| Method          | HTTP     | 설명                                               |
| --------------- | -------- | -------------------------------------------------- |
| exchange        | Anything | HTTP header setting 기능, ResponseEntity로 반환    |
| execute         | Anything | Request/Response 콜백을 수정 기능                  |
| getForObject    | GET      | Java Object로 반환                                 |
| getForEntity    | GET      | ResponseEntity로 반환                              |
| postForLocation | POST     | Java.net.URI로 반환                                |
| postForObject   | POST     | Java Object로 반환                                 |
| postForEntity   | POST     | ResponseEntity로 반환                              |
| delete          | DELETE   | HTTP DELETE 메서드 실행                            |
| headForHeaders  | HEAD     | 헤더의 모든 정보를 얻을 수 있으면 HTTP HEAD를 사용 |
| put             | PUT      | HTTP PUT 메서드 실행                               |
| patchForObject  | PATHC    | HTTP PATCH 메서드 실행                             |
| optinosForAllow | OPTIONS  | 주어진 URL 주소에서 지원하는 HTTP 메서드를 조회    |

</br >

위 메서드 중 `exchange()`를 써서 진행해 보겠습니다. `exchange()`는 HTTP method에 상관없이 사용할 수 있습니다.

~~~java
@Repository
@RequiredArgsConstructor
public class RestTemplateRepository<T> {

    private final RestTemplate restTemplate;

    public ResponseEntity<T> get(String url, HttpHeaders httpHeaders) {
        return call(url, GET, httpHeaders, null, (Class<T>) Object.class);
    }

    public ResponseEntity<T> get(String url, HttpHeaders httpHeaders, Class<T> response) {
        return call(url, GET, httpHeaders, null, response);
    }

    public ResponseEntity<T> post(String url, HttpHeaders httpHeaders, Class<T> response) {
        return call(url, POST, httpHeaders, null, response);
    }

    public ResponseEntity<T> post(String url, HttpHeaders httpHeaders, Object requestBody, Class<T> response) {
        return call(url, POST, httpHeaders, requestBody, response);
    }

    private ResponseEntity<T> call(String url, HttpMethod httpMethod, HttpHeaders httpHeaders, Object requestBody, Class<T> response) {
        return restTemplate.exchange(url, httpMethod, new HttpEntity<>(requestBody, httpHeaders), response);
    }
}
~~~

위 예제에서는 `@RequiredArgsConstructor`를 사용하여 RestTemplate를 주입받았습니다. 다양한 RestTemplate를 주입받고 싶을 때는 `@RequiredArgsConstructor`를 사용하지 밀고 생성자를 통해 직접 주입받으면 됩니다. [여기](https://roomenergy.tistory.com/6?category=876065)를 참고하시면 더 자세한 정보를 얻으실수 있습니다.

`RestTemplateRepository`를 보시면 제네릭으로 선언되어 있습니다. 제네릭으로 선언하면 HTTP 요청에 의한 응답을 다양한 타입으로 받아 처리할 수 있습니다.

</br >

### 3. 실제 사용 방법

예를 들어 email을 입력받아 해당 User Id를 찾는 API가 있다고 가정해보겠습니다.

**Response Body**

~~~java
@Getter
@NoArgsConstructor
public class ApiResponse {
    private User user;

    public String getUserId() {
        return this.user.getId();
    }

    @Getter
    @NoArgsConstructor
    private class User {
        private String id;
    }
}
~~~

</br >

다음으로 Api를 호출하는 Service를 만들겠습니다.

~~~java
@Service
@RequiredArgsConstructor
public class ApiCallService {

    // 생성자 주입시 제네릭 활용
    private final RestTemplateRepository<ApiResponse> restTemplateRepository;

    public ApiResponse call(String email) {
        HttpHeaders headers = new HttpHeaders();

        MultiValueMap<String, String> requestBody = new LinkedMultiValueMap<>();
        requestBody.add("email", email);

        return restTemplateRepository.post("test.url.com", headers, requestBody, ApiResponse.class)
                .getBody();
    }
}

~~~

입력받은 email은 `MultiValueMap`을 사용하여 requestBody로 만들어 줬습니다.

위 코드에서 생성자 주입시 제네릭을 활용했습니다. 이렇게하면 호출 결과로 온 `ResponseEntity`의 `getBody()`를 호출했을 때 응답 객체로 설정한 값으로 반환받을 수 있습니다.

생성자 주입시 초기값을 설정해 주지 않는다면 `Object.class`를 반환받아 원하는 응답 객체로 Casting을 해줘야 합니다.

