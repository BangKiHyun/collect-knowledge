# 싱글턴 패턴(Singleton pattern)

## 싱글턴 패턴이란?

전역 변수를 사용하지 않고 **객체를 하나만 생성 하도록 하여, 생성된 객체를 어디에서든지 참조할 수 있도록 하는 디자인 패턴입니다.**

**Singleton class는 하나의 인스턴스만을 생성하는 책임**이 있으며 getInstance() 같은 메서드를 통해 모든 클라이언트에게 동일한 인스턴스를 반환하는 작업을 수행합니다.

</br >

## 싱글턴 패턴의 필요성

싱글턴 패턴은 오직 하나의 인스턴스만 생성하기 때문에 메모리 낭비를 방지할 수 있습니다. 

그리고 여러 스레드가 동시에 해당 인스턴스를 공유하여 사용하게끔 할 수 있으므로, 요청이 많은 곳에서 사용하면 효율을 높일 수 있습니다.

## 싱글턴 만드는 방법 (Java 8)

### 1. public static final 방식

```java
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();

    private Singleton() {
    }
}
```

이 방법은 **static 영역이 로딩될 때 딱 1번만 호출되기 떄문에 전체 시스템에서 하나 뿐임을 보장하며, Thread-safe 합니다.** 즉, `정적 바인딩(static binding)`을 통해 해당 인스턴스를 메모리에 등록해서 사용합니다.

`final` 이기 때문에 절대로 다른 객체를 참조할 수 없으며, 굉장히 간결합니다.

싱글턴 클래스를 보면 `private` 기본 생성자가 있습니다. 해당 생성자를 만든 이유는 **클래스에 정의된 생성자가 하나도 없을 경우, 컴파일러는 자동적으로 기본 생성자를 추가하기 때문입니다.**

</br >

### 2. 정적 팩터리 방식 (Eager Loading)

~~~java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
~~~

이 방법은 정적 팩터리 메서드를 `public static` 멤버로 제공합니다. 정적 팩터리 메서드로 항상 같은 객체의 참조를 반환하므로 싱글턴을 보장해줍니다. 그리고 첫 번째 방식과 마찬가지로 `Thread-safe` 합니다.

이 방식은 첫 번째 방식에 비해 API 변경에 매우 유연하고, 메서드 레퍼런스로 `Singleton::getInstance` 처럼 사용이 가능합니다.

</br >

### 3. 정적 팩터리 방식 (Lazy Loading)

~~~java
public class Singleton {
    private static Singleton INSTANCE;

    private Singleton() {
    }

    public static synchronized Singleton getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new Singleton();
        }
        return INSTANCE;
    }
}
~~~

이 방법은 컴파일 시점에 인스턴스를 생성하는 것이 아니라, 인스턴스가 필요한 시점에 인스턴스를 생성합니다. 즉, `동적 바인딩(dynamic binding)`을 통해 인스턴스를 생성하는 방식입니다.

Lazy Loading 방식은 Thread-safe하지 않기 때문에 `synchronized` 키워드를 사용해 `Thread-safe` 하게 만들었습니다.

하지만 `synchronized`키워드를 사용하면 성능이 매우 떨어집니다.

</br >

### 4. 정적 팩터리 방식 (Lazy Loading, 성능저하 완화)

~~~java
public class Singleton {
    private volatile static Singleton INSTANCE;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
~~~

이 방법은 동기화 블럭 방식을 개선한  DCL(Double Checking Locking) 방식 입니다. 인스턴스가 생성되지 않은 경우에만 `synchronized`가 실행되게 구현하는 방식입니다.

### 5. Enum 방식

~~~java
public enum Singleton3 {
    INSTANCE;
}
~~~

이 방법은 첫 번째 방법과 비슷하지만, 더 간결합니다. 원소가 하나뿐인 열거 타입의 싱글턴을 만드는 가장 좋은 방법입니다.

주의할 점으로 만들려는 싱글턴이 Enum외의 클래스를 상속해야 한다면 사용할 수 없습니다.

