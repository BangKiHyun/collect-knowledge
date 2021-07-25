# [JPA] Auditing 기능

데이터베이스에 특정 데이터가 언제 생성되었고, 마지막 수정 날짜는 언제인지 기록하는 것은 꽤나 중요하다. 그렇기 때문에 해당 데이터는 도메인마다 공통으로 존재하게 되고, 코드의 중복이 발생하기 마련이다.

이런 문제를 해결하기 위해 JPA는 `Audit`라는 기능을 제공하고 있는데, 이는 JPA에서 시간에 대한 값을 자동으로 넣어주는 기능이다.

</br >

## BaseEntity 생성

Auditing이 필요한 Entity에서 사용할 BaseEntity는 다음과 같다.

~~~java
@Getter
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
public abstract class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime modifiedDate;
}
~~~

- `@EntityListeners(AuditingEntityListener.class)`: Entity를 DB에 적용하기 이전 이후에 커스텀 콜백을 요청할 수 있는 노테이션이다. 여기서는 해당 클래스에 Auditing 기능을 포함하게 한다.
- `@MappedSuperclass`: 객체 입장에서 공통 매핑 정보가 필요할때 사용하는 어노테이션이다. 공통으로 필요한 정보를 부모 클래스에 선언하고 속성만 상속받아 사용할 수 있다.
- `@CreatedDate`: 데이터 생성 날짜를 자동 저장하는 어노테이션이다.
- `@LastModifiedDate`: 데이터 수정 날짜를 자동 저장하는 어노테이션이다.

</br >

## Entity 적용

`BaseEntity`를 상속받을 Entity는 다음과 같다.

~~~java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class User extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "user_id")
    private Long id;

    @Column(name = "email")
    private String email;

    @Column(name = "name")
    private String name;
}
~~~

위와 같이 적용을 하면 `User` Entity는 `BaseEntity`가 갖고있는 `createdDate`, `modifiedDate`를 사용할 수 있게된다.

이제 JPA가 생성 일자와 수정 일자를 인식할 수 있도록 Auditing 기능을 활성화 해주자.

~~~java
@EnableJpaAuditing 
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
~~~

`@EnableJpaAuditing`어노테이션을 통해 JPA가 생성 일자와 수정 일자를 인식할 수 있게 했다.

해당 일자들은 트랜잭션 커밋 시점에 플러시가 호출할 때 하이버네이트가 자동으로 시간 값을 채워준다.

