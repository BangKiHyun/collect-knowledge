# [Spring Boot] Swagger Authorization header 설정

오늘은 Swagger에 Authorization header를 설정하는 방법을 알아보겠습니다.

Swagger에 header를 설정하는 방법을 2가지가 있습니다.

1. 각각의 API에 Header 정보를 추가하는 방법
2. 전역으로 관리하는 방법

두가지 방법을 모두 알아보겠습니다. 참고로 이번 포스트는 JWT 토큰을 통해 로그인 처리를 한다고 가정하고 진행합니다.

## 1. 각각의 API에 Header 정보를 추가하는 방법

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        ParameterBuilder parameterBuilder = new ParameterBuilder();
        parameterBuilder.name("Authorization") //헤더 이름
                .description("Access Token") //해당 파라미터에 대한 설명
                .modelRef(new ModelRef("string"))
                .parameterType("header")
                .required(false)
                .build();
        List<Parameter> parameters = new ArrayList<>();
        parameters.add(parameterBuilder.build());

        return new Docket(DocumentationType.SWAGGER_2)
                .globalOperationParameters(parameters)
                .useDefaultResponseMessages(false)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.toy.project"))
                .paths(PathSelectors.ant("/api/**"))
                .build();
    }
}
```

위와 같이 설정하면 Swagger에 Parameter가 추가된 것을 볼 수 있습니다.

![img](https://blog.kakaocdn.net/dn/cjzzfG/btq4H9sVxh0/1uD5Zq41RDFJBv62gOfLSk/img.png)

## 2. 전역으로 관리하는 방법

첫 번째 방법은 로그인 후 API요청시 매번 토큰값을 입력해줘야 하는 번거로움이 있습니다. 번거로움을 없애주고자 Swagger에서는 토큰값을 전역으로 관리할 수 있는 기능을 제공해주고 있습니다.

다음 방법을 사용하면 토큰값을 전역으로 관리해주기 때문에 매번 토큰값을 입력해주지 않아도 됩니다.

~~~java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {

        return new Docket(DocumentationType.SWAGGER_2)
                .useDefaultResponseMessages(false)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.toy.project"))
                .paths(PathSelectors.ant("/api/**"))
                .build()
                .securityContexts(Arrays.asList(securityContext()))
                .securitySchemes(Arrays.asList(apiKey()));
    }

    private SecurityContext securityContext() {
        return springfox
                .documentation
                .spi.service
                .contexts
                .SecurityContext
                .builder()
                .securityReferences(defaultAuth()).forPaths(PathSelectors.any()).build();
    }

    List<SecurityReference> defaultAuth() {
        AuthorizationScope authorizationScope = new AuthorizationScope("global", "accessEverything");
        AuthorizationScope[] authorizationScopes = new AuthorizationScope[1];
        authorizationScopes[0] = authorizationScope;
        return Arrays.asList(new SecurityReference("Bearer +Access Token", authorizationScopes));
    }

    private ApiKey apiKey() {
        return new ApiKey("Bearer +Access Token", "Authorization", "header");
    }
}
~~~

위와 같이 설정하면 Swagger에 자물쇠 모양이 생긴 것을 볼 수 있습니다.

![img](https://blog.kakaocdn.net/dn/bVG6F1/btq4MScgfcF/RHLBV1gjgd2YXqKHtWgdmk/img.png)



자물쇠 모양을 클릭하면 토큰값을 입력할 수 있는 화면이 나옵니다. 이 부분에 토큰값을 입력해주면 됩니다.

![img](https://blog.kakaocdn.net/dn/bCsloI/btq4H1hLs1M/7rQsHt7A64a2chcpwJMckk/img.png)

참고로 현재 토큰값은 **Bearer** **eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhQGEuYSIsImlhdCI6MTYyMDgwNjc5MSwiZXhwIjoxNjgzODc4NzkxfQ.zgdI0Fa_EacH_uAKLTZqSiO6esUW5szW-PhIjE-pcLw** 와 같은 형식으로 들어오기 때문에 토큰 값을 입력할 때 Bearer를 함께 입력해줘야 합니다.

