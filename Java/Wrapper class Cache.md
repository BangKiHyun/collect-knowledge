# Wrapper class Cache

Java에 애플리케이션의 성능 향상을 위한 Cache로직이 종종 사용된다.

실제로 Interger 클래스는 내부에서 Integer 사용을 위해 `IntegerCache`를 관리한다.

`Interger.class` 내부에 있는 `IntegerCache`

~~~java
public final class Integer extends Number implements Comparable<Integer> {
    
    ...
    
     private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
  
    ...
    
}
~~~

- 위 코드로 보았을 때 `low = -128, high = 127`로 캐시의 기본 범위는 `-128 ~ 127`
- 이 범위의 정수 객체를 캐싱하는 이유는 개발할 때 빈번하게 사용되기 때문이라 한다.

</br >

다음은 Integer 클래스 내부에 있는 `vallueOf` 메서드다.

~~~java
    @HotSpotIntrinsicCandidate
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
~~~

- 위 코드로 보았을 때 **입력된 매개변수 값이 IntegerCache 범위를 벗어나지 않으면 미리 만들어둔 Interger 값을, 아니면 새로운 Integer 인스턴스를 반환한다.**

</br >

## Integer값 비교

다음 코드의 결과값은 어떻게 될까?

~~~java
public class Main {
    public static void main(String[] args) {
        Integer firstNumber = -128;
        Integer secondNumber = -128;

        System.out.println(firstNumber == secondNumber); //동일성
        System.out.println(firstNumber.equals(secondNumber)); //동등성
    }
}
~~~

결론부터 말하자면 **동일성, 동등성 둘 다 true**다.

### why?

- Java는 모든 primitive types에 대해서 wrapper classes를 제공하며, **auto-boxing, auto-unboxing**을 지원한다.

- **Integer는 int형의 wrapper 클래스**로 다음과 같이 **auto-boxing이 적용**된다.

  ~~~java
  Integer firstNumber = Integer.valueOf(-128);
  ~~~

- -128 은 IntegerCache의 범위에 들어감으로 캐싱해둔 값을 반환하기 때문에 둘은 **같은 object**를 가리키게된다.

- 그러므로 동일성 비교(==)를 해도 true를 반환한다.

</br >

다음 코드의 결과값은 어떻게 될까?

~~~java
public class Main {
    public static void main(String[] args) {
        Integer firstNumber = 128;
        Integer secondNumber = 128;

        System.out.println(firstNumber == secondNumber); //동일성
        System.out.println(firstNumber.equals(secondNumber)); //동등성
    }
}
~~~

- 128 은 IntegerCache의 범위에 들어가지 않는다. 그러므로 같은 object를 가리키지 않는다.
  - new 연산자를 통해 새로운 오브젝트(Integer)가 생성된다. 즉, 둘의 주소가 달라진다.
- 그러므로 **동일성 비교(==)는 false, 동등성 비교는 true를 반환**한다.

</br >

### 정리

- 자주 사용하는 객체를 캐싱하여 이미 있는 객체가 다시 생성되지 않도록 할 수 있다. 그러므로 메모리 요구량과 쓰레기 수집비용을 줄일 수 있다.
- 하지만 위에서 본 것 처럼 의도치 않은 결과를 발생시킬 수 있다.

