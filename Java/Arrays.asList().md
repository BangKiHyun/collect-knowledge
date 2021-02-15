# Arrays.asList()

코드를 구현하던 중 Arrays.asList()로 생성한 List객체에 새로운 값을 추가할 때 **UnsupportedOperationException**이 발생했습니다. 그래서 Arrays.asList()에 대해 알아보려 합니다.

</br > 

## Arrays.asList()

Arrays.asList()는 AbstracList를 상속받은 private static class인 ArraysList를 반환합니다.

![img](https://blog.kakaocdn.net/dn/bpELxy/btqW1tl3MQk/yxIcBZjQzF3zIzl4zxzMRK/img.png)

이 클래스는 set(), get(), contains() 메서드를 제공하고 있습니다.

하지만 add 메서드 호출 시에는 AbstractList가 제공하는 메서드를 호출하여 **UnsupportedOperationException이 발생하게 됩니다.**

즉, asList()로 생성한 객체의 구조적 변경 시 **UnsupportedOperationException을 발생시키게 됩니다.(add, remove)**

```java
public class asListTest {
    @DisplayName("asList()로 생성한 List 객체에 값 추가할 떄 Exception 발생")
    @Test(expected = UnsupportedOperationException.class)
    public void asListExceptionTest() {
        List<String> asList = Arrays.asList("A", "B", "C");
        asList.add("D");
    }
}
```

![img](https://blog.kakaocdn.net/dn/KxwrP/btqXu5wrj03/0PgipcwvOhxWBmf0Jej3H0/img.png)

</br >

## asList 값 변경

다음은 String 배열에 담겨있는 값을 asList()를 사용해 List로 바꾼 코드입니다.

```java
    public static void main(String[] args) {
        String[] alphabet = {"A", "B", "C"};
        List<String> asList = Arrays.asList(alphabet);

        for (String value : asList) {
            System.out.print(value + " "); // A B C
        }
    }
```

만약 여기서 원본 데이터(String 배열)를 수정하면 어떻게 될까요?

```java
    public static void main(String[] args) {
        String[] alphabet = {"A", "B", "C"};
        List<String> asList = Arrays.asList(alphabet);

        String newAlphabet = "D";
        alphabet[0] = newAlphabet;
        
        for (String value : asList) {
            System.out.print(value + " "); // D B C
        }
    }
```

**위 코드를 보면 원본 데이터(String 배열)을 수정했는데 asList()로 생성한 List의 값까지 변경된 걸 알 수 있습니다.**

즉, asList()를 사용해서 List 객체를 만들때 **원본 데이터의 주소값을 가져옵니다.**

 <br >

그렇다면 asList()를 사용한 List 객체의 값을 변경하면 원본 데이터도 바뀌는지 확인해 보겠습니다.

```java
    public static void main(String[] args) {
        String[] alphabet = {"A", "B", "C"};
        List<String> asList = Arrays.asList(alphabet);

        String newAlphabet = "D";
        asList.set(0, newAlphabet);

        System.out.println(alphabet[0]); // D

    }
```

원본 데이터의 값이 변경됐습니다. 이로써 주소값을 가져오는 것이 확실한 것 같습니다.

 

## 정리

- Arrays.asList()로 생성한 객체는 구조적 병경 시 UnsupportedOperationException을 발생시킵니다.(add, remove)
- 값 변경 시 원본 데이터의 주소값을 가져옵니다.

