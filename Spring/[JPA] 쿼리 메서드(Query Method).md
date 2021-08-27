# [JPA] 쿼리 메서드(Query Method)

Spring Data JPA에서 제공하는 쿼리 메서드 기능으로 3가지가 있다.

1. 메서드 이름으로 쿼리 생성
2. JPA NamedQuery 사용
3. @Query 어노테이션 사용

이 중에 메서드 이름으로 쿼리를 생성하는 방법을 알아보겠다.

</br >

## 메서드 이름으로 쿼리 생성이란?

메서드 이름을 분석해서 JPQL 쿼리를 실행하는 것을 의미한다.

똑똑한 Spring Data JPA가 메서드 이름을 분석해서 JPQL을 유추해서 생성하고 실행한다.

</br >

```java
public interface UserRepository extends Repository<User, Long> {
  List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```

위 코드는 다음과 같은 쿼리를 생성한다.

`select u from User u where u.emailAddress = ?1 and u.lastname = ?2`

</br >

다음 표는 JPA에 지원되는 키워드 및 설명이다.

| Keyword                | Sample                                                       | JPQL snippet                                                 |
| :--------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| `Distinct`             | `findDistinctByLastnameAndFirstname`                         | `select distinct … where x.lastname = ?1 and x.firstname = ?2` |
| `And`                  | `findByLastnameAndFirstname`                                 | `… where x.lastname = ?1 and x.firstname = ?2`               |
| `Or`                   | `findByLastnameOrFirstname`                                  | `… where x.lastname = ?1 or x.firstname = ?2`                |
| `Is`, `Equals`         | `findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1`                                   |
| `Between`              | `findByStartDateBetween`                                     | `… where x.startDate between ?1 and ?2`                      |
| `LessThan`             | `findByAgeLessThan`                                          | `… where x.age < ?1`                                         |
| `LessThanEqual`        | `findByAgeLessThanEqual`                                     | `… where x.age <= ?1`                                        |
| `GreaterThan`          | `findByAgeGreaterThan`                                       | `… where x.age > ?1`                                         |
| `GreaterThanEqual`     | `findByAgeGreaterThanEqual`                                  | `… where x.age >= ?1`                                        |
| `After`                | `findByStartDateAfter`                                       | `… where x.startDate > ?1`                                   |
| `Before`               | `findByStartDateBefore`                                      | `… where x.startDate < ?1`                                   |
| `IsNull`, `Null`       | `findByAge(Is)Null`                                          | `… where x.age is null`                                      |
| `IsNotNull`, `NotNull` | `findByAge(Is)NotNull`                                       | `… where x.age not null`                                     |
| `Like`                 | `findByFirstnameLike`                                        | `… where x.firstname like ?1`                                |
| `NotLike`              | `findByFirstnameNotLike`                                     | `… where x.firstname not like ?1`                            |
| `StartingWith`         | `findByFirstnameStartingWith`                                | `… where x.firstname like ?1` (parameter bound with appended `%`) |
| `EndingWith`           | `findByFirstnameEndingWith`                                  | `… where x.firstname like ?1` (parameter bound with prepended `%`) |
| `Containing`           | `findByFirstnameContaining`                                  | `… where x.firstname like ?1` (parameter bound wrapped in `%`) |
| `OrderBy`              | `findByAgeOrderByLastnameDesc`                               | `… where x.age = ?1 order by x.lastname desc`                |
| `Not`                  | `findByLastnameNot`                                          | `… where x.lastname <> ?1`                                   |
| `In`                   | `findByAgeIn(Collection<Age> ages)`                          | `… where x.age in ?1`                                        |
| `NotIn`                | `findByAgeNotIn(Collection<Age> ages)`                       | `… where x.age not in ?1`                                    |
| `True`                 | `findByActiveTrue()`                                         | `… where x.active = true`                                    |
| `False`                | `findByActiveFalse()`                                        | `… where x.active = false`                                   |
| `IgnoreCase`           | `findByFirstnameIgnoreCase`                                  | `… where UPPER(x.firstname) = UPPER(?1)`                     |

</br >

## 주의 할 점

주의할 점으로는 필터 조건에 부합하지 않는 키워드는 사용할 수 없다.

그리고 `Entity`에 존재하지 않는 `Property Type` 을 사용하게 되면 오류가 발생한다.

다음은 Room Entity 코드이다.

```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Room {

    @Id
    @Column(name = "room_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name")
    private String name;

    @Enumerated(value = STRING)
    @Column(name = "status")
    private RoomStatus status;
}
```

`Room`과 관련된 `JpaRepository`를 다음과 같이 만들어 주고, 메서드를 정의해줬다.

```java
public interface RoomRepository extends JpaRepository<Room, Long> {
    Room findByAddress(String address);
}
```

위에 정의한 메서드를 통해 JPA가 쿼리를 자동으로 생성해 줘야 하지만 `Room Entity`안에는 `Address`라는 `Property`가 존재하지 않기 때문에 오류가 발생한다.

![image](https://user-images.githubusercontent.com/43977617/131121511-72797e60-9f9c-48eb-9515-5529a392e9c2.png)

</br >

## 참고

- https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation

