# [Spring Boot] @NotNull, @NotEmtpy, @NotBlank 차이점

API를 요청할 때 request body로 들어는 paramter에 대해 문자열 검증하는 방법으로 @NotNull, @NotEmpty, @NotBlank가 있습니다.

이 3가지 Annotation은 비슷해 보이지만 조금씩 다릅니다. 오늘은 이 3가지 Annotation의 차이점에 대해 확실하게 알고 넘어가 보겠습니다.

해당 포스트에서 사용할 DTO는 다음과 같습니다.

```java
@Getter
@NoArgsConstructor
public class ValidationDto {

    @NotNull
    private String notNull;

    @NotEmpty
    private String notEmpty;

    @NotBlank
    private String notBlank;
}
```

</br >

## @NotNull

@NotNull은 말 그대로 `null`만 허용하지 않고, `""`, `" "`는 허용합니다.

즉, 초기화된 String(`""`)이나 공백(`" "`)을 허용하지 않는다면 사용해선 안됩니다.

Postman을 통해 확인해보겠습니다.

![img](https://blog.kakaocdn.net/dn/blfbTv/btq4XbCLaH5/HEhKL1aY5cpsc8xB5Jqc3k/img.png)

모든 값을 `null`로 넣어줬습니다. 3가지 Validation 모두 오류가 발생한 것을 볼 수 있습니다.

</br >

## @NotEmpty

@NotEmty는 `null`과 `""`를 허용하지 않고, `" "`는 허용합니다.

@NotNull에 `""`가 추가된 것입니다.

![img](https://blog.kakaocdn.net/dn/cpwQp1/btq4XFcuIFY/ExrG9vNiYPGKszv0PmMUK0/img.png)

초기화된 String(`""`)으로 값을 넣어줬습니다.

@NotNull에 대한 Validation은 통과하지만 @NotEmpty와 @NotBlank는 통과하지 못했습니다.

</br >

## @NotBlank

@NotBlank는 `null`, `""`, `" "`모두 허용하지 않습니다.

![img](https://blog.kakaocdn.net/dn/J991E/btq4XgcWFDb/dq8Y9yZTDsps5WTd62yoqk/img.png)

공백(`" "`)으로 값을 넣어줬습니다.

@NotNull, @NotEmpty에 대한 Validation은 통과하지만 @NotBlank는 통과하지 못한 걸 알 수 있습니다.

</br >

정리하면 다음과 같습니다.

|           | Null | ""   | " "  |
| --------- | ---- | ---- | ---- |
| @NotNull  | X    | O    | O    |
| @NotEmpty | X    | X    | O    |
| @NotBlank | X    | X    | X    |

</br >

## 참고

- https://www.baeldung.com/java-bean-validation-not-null-empty-blank
- https://jyami.tistory.com/55