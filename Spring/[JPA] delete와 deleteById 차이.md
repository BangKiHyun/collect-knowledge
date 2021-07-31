# [JPA] delete와 deleteById 차이

Spring Data Jpa를 사용해서 토이 프로젝트를 구성하던 중에 `delete`와 `deleteById`중 뭘 사용하면 좋을지 고민해 보다가 둘의 차이점을 알아보기로 했다.

우선 `delete`와 `deleteById`는 `CrudRepository`인터페이스 안에 다음과 같이 정의되어 있다.

![image](https://user-images.githubusercontent.com/43977617/127503937-61e754df-b00b-4f0b-a02e-876dad54df15.png)

</br >

`delete`와 `deleteById`의 실제 구현체는 `SimpleJpaRepository`이다.

`SimpleJpaRepository`에 구현되어있는 `delete`는 다음과 같다.

![image](https://user-images.githubusercontent.com/43977617/127730132-66656d38-df0e-4899-ae1f-7e74b22167df.png)

`delete`는 입력받은 `entity`에 대해 `null`체크를 한 후 `entity`에 대한 삭제를 진행하고 있다.

자 이제 `deleteById`를 봐보자.

![image](https://user-images.githubusercontent.com/43977617/127729950-6624c302-c6ce-44b0-b9d7-5ecaf2600f3b.png)

구현부를 보면 `delete()`메서드를 호출하고 있는 모습을 볼 수 있다.

`findById()`로 해당 entity를 찾고 `delete()`를 호출한다.

`findById()`로 해당 `entity`를 찾지 못한다면 `EmptyResultDataAccessException`을 발생시킨다.

여기서 중요한 점은 `deleteById`는 결국 `delete`를 호출하여 삭제를 진행한다는 점이다. 즉, `delete`와 `deleteById`의 성능 차이는 크게 없어보인다.

</br >

그렇다면 둘은 왜 나뉘어져 있을까?

일단 `deleteById`는 해당 메서드 만으로 `entity`조회 및 삭제가 다 된다는 장점이 있다.

`delete`는 보통 `findById`와 같이 사용한다. `findById`와 같이 사용하면 예외처리를 커스텀하여 원하는 메시지를 보낼 수 있다는 장점이 있다.

```java
public void delete(Long roomId) {
    roomRepository.delete(
            roomRepository.findById(roomId)
                    .orElseThrow(() -> new RoomNotFoundException(String.format("Not found room. room id: %d", roomId))));
}
```

위 코드를 보면 `RoomNotFoundException`을 사용하고, 원하는 메시지를 작성하여 클라이언트에게 보낸다.

</br >

`deleteById`와 `delete` 둘의 성능 차이는 크게 안나기 때문에 상황에 맞춰 원하는 걸 사용하면 될 것 같다.

