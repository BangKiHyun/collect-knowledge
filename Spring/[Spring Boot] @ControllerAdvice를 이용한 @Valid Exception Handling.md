# [Spring Boot] @ControllerAdvice를 이용한 @Valid Exception Handling

오늘은 @ControllerAdvice를 이용해서 @Valid시 발생하는 에러를 처리해보겠습니다.

@Valid 사용법은 [여기](https://roomenergy.tistory.com/39)에 자세히 나와있습니다.

</br >

### @ControllerAdvice란?

@ControllerAdvice는 **@Controller 전역**에서 발생할 수 있는 예외를 잡아 처리해주는 애너테이션입니다.

</br >

자 그럼 이제 코드를 통해 알아보겠습니다.

### ExceptionController.class

```java
@RestControllerAdvice
public class ExceptionController {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, ErrorResponse>> handelValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, ErrorResponse> errorMap = new HashMap<>();
        ex.getBindingResult().getAllErrors()
                .forEach(e -> errorMap.put(((FieldError) e).getField(), makeErrorResponse(e.getCode(), e.getDefaultMessage())));
        return ResponseEntity.badRequest()
                .body(errorMap);
    }

    private ErrorResponse makeErrorResponse(String validationCode, String detail) {
        ErrorCode errorCode = ErrorCode.findByValidationCode(validationCode);
        return new ErrorResponse(errorCode.getCode(), errorCode.getDescription(), detail);
    }
}
```

@Valid의 유효성을 통과하지 못하면, **MethodArgumentNotValidException**이 발생합니다. 그렇기 때문에 해당 클레스에서는 `@ExceptionHandler` 애너테이션으로 `MethodArgumentNotValidException.class`에 대한 에러를 잡아 처리합니다.

ResponseEntity 값은 Error가 발생한 Field 값(Stsring)과 직접 만든 에러 메시지(ErrorResponse)를 Map 형태로 만들어서 넣어주었습니다.

Map을 사용한 이유는 @Valid시 발생한 에러를 모두 리턴할 수 있도록 하기 위함입니다.

</br >

### ErrorResponse.class

```java
@Getter
public class ErrorResponse {

    private String code;
    private String description;
    private String detail;

    public ErrorResponse(String code, String description) {
        this.code = code;
        this.description = description;
    }

    public ErrorResponse(String code, String description, String detail) {
        this.code = code;
        this.description = description;
        this.detail = detail;
    }
}
```

해당 클래스는 API에 내려줄 에러 형태를 정의한 클래스입니다.

- code: Enum을 이용하여 정의한 에러 코드를 알려줍니다.
- description: 어떤 에러인지 알려줍니다.
- detail: 에러의 세부 내용을 알려줍니다.

</br >

### ErrorCode.class(Enum)

```java
public enum ErrorCode {
    NOT_NULL("NotNull", "ERROR_CODE_0001", "필수값이 누락되었습니다."),
    EMAIL("Email", "ERROR_CODE_0002", "올바른 이메일 형식이 아닙니다.");

    private String validationCode;
    private String code;
    private String description;

    ErrorCode(String validationCode, String code, String description) {
        this.validationCode = validationCode;
        this.code = code;
        this.description = description;
    }

    public static ErrorCode findByValidationCode(String validationCode) {
        return Arrays.stream(values())
                .filter(errorCode -> errorCode.validationCode.equals(validationCode))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("유효하지 않은 검증 코드입니다."));
    }

    public String getCode() {
        return code;
    }

    public String getDescription() {
        return description;
    }
}
```

해당 클래스는 에러에 대한 코드 및 어떤 에러인지를 정의하고, 간단한 에러 설명을 넣었습니다.

validationCode를 이용해 @Email, @NotNull 등 어떤 에러인지 찾고, 해당 에러에 맞는 코드와 설명을 알려줍니다.

</br >

이제 간단한 컨트롤러와 DTO를 만들어서 위 코드가 잘 돌아가는지 확인해보겠습니다.

### UserController.class

```java
@RestController
public class UserController {

    @PostMapping("/user")
    public ResponseEntity<String> saveUser(final @Valid @RequestBody UserSaveRequest userSaveRequest) {
        return ResponseEntity.ok()
                .body("유저 검증 성공");
    }
}
```

### UserSaveRequest.class

```java
@Getter
@NoArgsConstructor
public class UserSaveRequest {

    @NotNull(message = "이름을 입력해 주세요.")
    private String name;

    @Email
    private String email;
}
```

DTO를 보면 간단하게 name과 email에 대한 검증을 합니다.

API 호출은 Posman을 이용해보겠습니다. Valid가 잘 적용되는지 확인하기 위해, name값을 넣지 않고, email값을 이상하게 줘보겠습니다.

![img](https://blog.kakaocdn.net/dn/bdx0ND/btq3Cz04v5T/QJHMYx4R9hSwjOiUsF2nP1/img.png)

Response값을 보면, @Valid를 통과하지 못한 모든 필드 값에 대한 에러와, 커스텀한 에러 메시지가 잘 반영되었음을 알 수 있습니다.

