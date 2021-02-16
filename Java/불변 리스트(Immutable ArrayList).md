# 불변 리스트(Immutable ArrayList)

불변 리스트를 생성하는 방법을 알아보기에 앞서 불변 객체가 주는 장점을 알아보겠습니다.

</br >

## Immutable Object 장점

- 불변 객체는 생성된 시점의 상태를 파괴될 때까지 간직하고 있으므로 Thread Safe 하여 따로 동기화할 필요가 없습니다.
- 불변 객체는 어떤 스레드도 다른 스레드에게 영향을 줄 수 없으므로 안심하고 공유할 수 있습니다.
- 불변 객체는 그 자체로 *실패 원자성을 제공합니다. 즉, 상태가 절대 변하지 않으므로 잠깐이라도 불일치 상태에 빠질 가능성이 없습니다.
  - 실패 원자성: 메서드에서 예외가 발생한 후에도 그 객체는 여전히(메서드 호출 전과 똑같은) 유효한 상태여야 한다.

</br >

## Immutable ArrayList 만드는 방법

### 1. Collections.unmodifiableList()

첫 번째 방법으로 Collections.unmodifiableList()를 사용하는 방법이 있습니다.

```java
    private void unmodifiableListExam() {
        String[] alphabet = {"A", "B", "C"};
        List<String> unmodifiableList = Collections.unmodifiableList(Arrays.asList(alphabet));
        for (String unmodifiableValue : unmodifiableList) {
            System.out.print(unmodifiableValue + " "); // A B C
        }
    }
```

다음 테스트 코드를 통해 Collections.unmodifiableList()로 만든 List가 불변인지 확인해 보겠습니다.

```java
public class ImmutableListTest {

    @Test(expected = UnsupportedOperationException.class)
    public void unmodifiableListTest() {
        String[] alphabet = {"A", "B", "C"};
        List<String> immutableList = Collections.unmodifiableList(Arrays.asList(alphabet));

        immutableList.set(0, "D");
    }
}
```

![img](https://blog.kakaocdn.net/dn/dVZZVw/btqXgpCPL9c/ERjL6QqaNki71ZKLPkTxak/img.png)

위와 같이 테스트가 잘 통과된 것을 확인할 수 있습니다.

 </br >

### 2. List.of() (Java 9)

두 번째 방법으로 List.of()를 사용하는 방법이 있습니다.

이 방법은 Java 9 버전부터 사용할 수 있습니다.

```java
    private void listOfExam() {
        String[] alphabet = {"A", "B", "C"};
        List<String> immutableList = List.copyOf(Arrays.asList(alphabet));
        for (String immutableValue : immutableList) {
            System.out.print(immutableValue + " "); // A B C
        }
    }
```

다음 테스트 코드를 통해 List.of()로 만든 List가 불변인지 확인해 보겠습니다.

```java
public class ImmutableListTest {

    @Test(expected = UnsupportedOperationException.class)
    public void ListOfTest() {
        String[] alphabet = {"A", "B", "C"};
        List<String> immutableList = List.of(alphabet);

        immutableList.set(0, "D");
    }
}
```

![img](https://blog.kakaocdn.net/dn/XhvqZ/btqW8GLXmot/0qWyDUAK4EYCxSa25eNY70/img.png)

</br >

## Collections.unmodifiableList() vs List.of()

Collections.unmodifiableList()를 사용하면 Intellij에서 다음과 같이 List.of()를 사용할 것을 추천합니다.

![img](https://blog.kakaocdn.net/dn/mGksP/btqXrCIbKWD/OkOBjzxR9rfMOzbTkOw2Xk/img.png)

지금부터 코드를 통해 둘의 차이를 알아보겠습니다.

</br > 

### Collections.unmodifiableList()

```java
    private void unmodifiableListTest() {
        String[] alphabet = {"A", "B", "C"};
        final List<String> unmodifiableList = Collections.unmodifiableList(Arrays.asList(alphabet));

        // 원본 데이터 변환
        alphabet[0] = "D";

        for (String unmodifiableValue : unmodifiableList) {
            System.out.print(unmodifiableValue + " "); // D B C
        }
    }
```

위 코드는 원본 데이터(String 배열)을 바꿨는데 Collections.unmodifiableList()로 생성한 List의 값도 같이 변경된 것을 알 수 있습니다.

즉, 원본 데이터를 바꿈으로써 완벽한 Immutable을 구현하지 못했다는 것을 알 수 있습니다.

또 하나 알 수 있는 것은 원본 객체의 주소값을 가져오는 것 같습니다.

 </br >

### List.of()

```java
    private void listOfTest() {
        String[] alphabet = {"A", "B", "C"};
        List<String> immutableList = List.copyOf(Arrays.asList(alphabet));

        // 원본 데이터 변환
        alphabet[0] = "D";

        for (String immutableValue : immutableList) {
            System.out.print(immutableValue + " "); // A B C
        }
    }
```

List.of()로 생성한 List 값은 원본 데이터가 변경되어도 영향을 끼치지 않음을 알 수 있습니다.

이렇게 둘의 차이를 비교해 본다면 Collections.unmodifiableList() 보다 List.of()가 좀 더 Immutable 한 것을 알 수 있습니다.