# [Java] String vs StringBuilder vs StringBuffer

## String

`String` 클래스는 immutable(불변) 하기 때문에 `String`의 값은 한 번 생성되면 변경될 수 없다.

`String` 클래스의 문자열을 저장하는 `char[]`를 보면 `final`로 선언되어있다는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/43977617/132988311-fd548de2-a7e2-44bd-ab65-3d557b22e7d4.png)

그렇기 때문에 한번 할당한 문자열에 대한 변경이 불가능하며, 문자열 **연산이 많아질 때** 계속해서 새로운 객체를 만들기 때문에, 오버해드가 발생하므로 성능이 떨어지는 단점이 있다.

반복적으로 문자열을 이어 붙이면 Heap영역에 참조를 잃은 문자열 객체가 계속해서 쌓이게 되고, 이는 GC에 대한 비용이 증가라는 단점을 낫게 된다.

하지만 이러한 불변성은 멀티스레드 환경에서 동기화를 신경쓸 필요가 없다. 즉, Thread Safe하다. 또한, 단순하게 읽어가는 조회연산에서는 타 클래스보다 빠르게 읽을 수 있는 장점이 있다.

### 결론

`String`은 문자열 연산이 적고 조회가 많을 때 멀티쓰레드 환경에서 사용하면 좋다.

</br >

## StringBuilder

`StringBuilder` 클래스는 mutable(변경가능)하다.

![image](https://user-images.githubusercontent.com/43977617/132988556-fbe07d4c-3f5b-4f50-a422-7a78930b434d.png)

위 그림을 보면 `StringBuilder`는 `AbstractStringBuilder`를 상속받고 있다. `AbstractStringBuilder`안에 있는 `append()` 메서드는 다음과 같이 되어있다.

![image](https://user-images.githubusercontent.com/43977617/132988635-0a9efab5-da3e-40f0-8a40-ab9e8e364786.png)

`append()` 메서드를 보면 연산이 필요할 때 크기를 변경시켜 문자열을 변경시킨다.

즉, `StringBuilder`는 처음에 new 연산을 통해 한번만 만들고, 문자열 연산이 일어나더라고 객체를 새로 만들지 않으므로 문자열 연산이 자주 있을 때 사용하면 성능이 좋다.

다만, StringBuilder는 동기화를 지원하지 않기 때문에 멀티스레드 환경에서 Thread Safe하지 않다.

### 결론

`StringBuilder`는 문자열 연산이 많을 때 스레드를 신경쓰지 않아도 되는 싱글스레드 환경에서 사용하면 좋다.

</br >

## String vs StringBuilder

 `String`의 성능 이슈를 개선하기 위해 JDK 1.5 이상에서는 컴파일 단계에서 내부적으로 `StringBuilder`로 변경되어 동작한다.

그러므로 `+`연산과 `append()` 메서드의 실질적인 성능 차이는 거의 없다. 다만 다음과 같은 코드는 조심해야한다.

~~~java
        String str = "";
        for (int count = 0; count < 100_000; count++) {
            str += count;
        }
~~~

위 코드는 매 루프마다 `StringBuilder` 객체가 생성되므로 기존 `+` 연산과 마찬가지로 불필요한 객체가 쌓이게 된다.

![image](https://user-images.githubusercontent.com/43977617/132989033-aca297fa-f37a-4602-a467-413395612578.png)

인텔리제이에서도 `StringBuilder`로 바꾸는걸 권유한다. 이럴때는 `StringBuilder`를 사용하자.

</br >

## StringBuffer

`StringBuffer` 클래스는 `StringBuilder` 와 같이 mutable(변경가능) 하다.

다만 `StringBuilder` 와의 차이점으로는 `StringBuffer` 는 `synchronized`가 가능하므로 멀티스레드 환경에서 Thread Safe하다.

![image](https://user-images.githubusercontent.com/43977617/132989365-658ba619-2be3-467d-a99e-e8f3c955d65a.png)

### 결론

`StringBuffer`는 문자열 연산이 많을 때 멀티스레드환경에서 사용하면 좋다.


