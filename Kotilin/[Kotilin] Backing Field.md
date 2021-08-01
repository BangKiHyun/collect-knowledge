# [Kotilin] Backing Field

## Backing Field

Backing Field는 `field`키워드를 사용하여 접근할 수 있다.

`field`키워드는 setter나 getter에서 사용할 수 있고, `field`키워드는 Backing Field를 참조하고 있다.

무슨소리인지 잘 모르겠다. 코드를 통해 확인해보자.

~~~kotlin
class BackingTest {
    var number = 0
        get() {
            return field
        }
        set(value) {
            field = value
        }
}
~~~

위 코드의 `number`프로퍼티에 정의된 `get()`과 `set()`을 보면 `field`키워드를 볼 수 있다. 여기서의 `field`키워드는 `number`의 대리자 역할을 하고 있다.

위 코드를 java코드로 변환하면 다음과 같다.

```java
public final class BackingTest {
   private int number;

   public final int getNumber() {
      return this.number;
   }

   public final void setNumber(int value) {
      this.number = value;
   }
}
```

`getter`와 `setter`를 보면 코틀린에서 사용한 `field`키워드가 `number` 필드를 가르키고 있는걸 볼 수 있다. 여기서 `number`를 필드를 코틀린에서 Backing Field라 부른다.

그렇다면 왜 Backing Field가 필요할까? 코틀린에서 `field`키워드를 사용하지 않으면 어떤 일이 발생할까?

다음 코드를 봐보자.

~~~kotlin
class BackingTest {
    var number = 0
        set(value) {
            number = value * 2
        }
}
~~~

위 코드에서 `field`키워드 대신 `number`프로퍼티를 사용했다. 이 코드를 java코드로 변환하면 다음과 같다.

```java
public final class BackingTest {
   private int number;

   public final int getNumber() {
      return this.number;
   }

   public final void setNumber(int value) {
      this.setNumber(value * 2); //setter call
   }
}
```

코드를 봐보면 `number`필드에 값을 주입하지 않고, setter call을 실행한다.

즉 `field`키워드를 사용했을 때는 number값에 직접 값을 주입했는데, `field`키워드를 사용하지 않았을 때는 setter call을 사용하는 모습을 볼 수 있다.

만약 이렇게 되면 setter를 무한으로 호출하기 떄문에 무한 재귀에 빠져 Stack Overflow가 날 것이다.

~~~kotlin
fun main() {
    val test = BackingTest()
    test.number = 2
}
~~~

![image](https://user-images.githubusercontent.com/43977617/127734573-55dc4e55-5074-4cf4-b34a-9da01afa6e31.png)

실제로 `number`의 값을 바꾸면 위와 같이 `StackOverflowError`가 발생한다.

