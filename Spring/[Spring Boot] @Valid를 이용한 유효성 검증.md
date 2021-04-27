# [Spring Boot] @Valid를 이용한 유효성 검증

 Spring Boot를 이용해서 @Valid 어노테이션을 이용한 유효성 검증 방법을 알아보려 합니다. @Valid를 이용하면, service 단이 아닌 객체 안에서, 들어오는 값에 대한 검증을 할 수 있습니다.

Jakarta Bean Validation에서 제공하는 검증 어노테이션을 이용하겠습니다. 해당 어노테이션을 사용하기 위해서는 다음 의존성을 추가해줘야 합니다.

```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

</br >

## @Valid 사용 방법

유효성 검증을 위한 간단한 DTO를 작성해 보겠습니다.

~~~java
@Getter
@NoArgsConstructor
public class UserSaveRequest {

    @NotNull
    private String name;

    @Email
    private String email;
}
~~~

위 예제는 Bean Validation에서 제공하는 어노테이션을 사용하여 이름과 이메일에 대한 필드를 제한했습니다.

- @NotNull: Null만 허용하지 않습니다.
- @Email: 이메일 형식이 아닌 경우 예외를 던집니다.

</br >

이제 간단한 컨트롤러를 작성해 보겠습니다.

~~~java
@RestController
public class ValidationTestController {

    @PostMapping("/user")
    public ResponseEntity<String> saveUser(final @Valid @RequestBody UserSaveRequest userSaveRequest) {
        return ResponseEntity.ok()
                .body("유저 검증 성공");
    }
}
~~~

`@Valid`를 `@RequestBody` 어노테이션 옆에 작성하게 되면 RequestBody로 들어오는 객체에 대한 검증을 수행합니다.

</br >

테스트를 위해 API를 손쉽게 호출할 수 있는 Postman을 이용해보겠습니다.

![image](https://user-images.githubusercontent.com/43977617/116193579-8c785380-a76a-11eb-8d45-87322e8f138f.png)

email값을 이상하게 줬더니 위와 같은 오류가 나왔습니다. 이를 통해 @Valid와 @Email 검증 어노테이션이 잘 동작한다는걸 알 수 있습니다.

## @ControllerAdvice를 이용한 에러 처리

@Valid를 이용하여 유효성 검증을 했을 때, 유효하지 않은 값이 들어오게 된다면 굉장히 많은 에러 메시지를 보여줍니다.

에러 메시지를 통째로 알려주게 된다면 시스템 내부 정보가 유출될 수 있고, 클라이언트단에서 해당 응답을 처리할 때 굉장히 힘들 수 있습니다.

이럴때 `@ControllerAdvice`를 이용해서 costom한 errohandling을 할 수 있습니다.

~~~java
@RestControllerAdvice
public class ExceptionController {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, ErrorResponse>> handelValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, ErrorResponse> errorMap = new HashMap<>();
        ex.getBindingResult().getAllErrors()
                .forEach(e -> errorMap.put(((FieldError) e).getField(), findErrorResponse(e.getCode(), e.getDefaultMessage())));
        return ResponseEntity.badRequest()
                .body(errorMap);
    }

    private ErrorResponse findErrorResponse(String validationCode, String detail) {
        ErrorCode errorCode = ErrorCode.findByValidationCode(validationCode);
        return new ErrorResponse(errorCode.getCode(), errorCode.getDescription(), detail);
    }
}
~~~

ResponseEntity 값으로, Error가 발생한 Filed 값(String)과, 에러 메시지(ErrorResponse.class)를 Map 형태로 만들어서, Response로 넣어줬습니다.

@ControllerAdvice를 이용한 예외 처리를 하는 방법은 여기에 자세히 나와있습니다.

이제 Postman 사용해서 다시 확인해 보겠습니다.

![image](https://user-images.githubusercontent.com/43977617/116198610-204d1e00-a771-11eb-98ba-3bb72923543b.png)

RequestBody를 @NotNull과 @Email 모두 validation이 안되도록 작성했습니다.

통과하지 못한 필드와 그에 대한 에러 메시지가 잘 반영되었음을 알 수 있습니다.

