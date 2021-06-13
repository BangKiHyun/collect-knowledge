# [Java] 강한 참조(Strong Reference)와 약한 참조(Weak Reference)

## 강한 참조(Strong Reference)

- 강한 참조는 Java의 기본 참조 유형으로 new를 통해 객체를 생성할 때 생기게 되는 참조다.
- 강함 참조를 통해 참조되고 있는 객체는 참조가 해제되지 않는 이상 가비지 컬렉션의 대상에서 제외된다.

## 약한 참조(Weak Reference)

- 약한 참조는 java의 lang 패키지의 WeakReference 클래스를 사용하여 생성한다.
- 약한 참조는 GC가 발생하면 무조건 수거된다.
- WeakReference가 사라지는 시점이 GC의 실행 주기와 일치한다.

## Soft Reference

- Soft 참조는 강한 참조와 약한 참조와는 다르게 GC에 의해 수거될 수도 있고, 수거되지 않을 수도 있다.
- 메모리에 충분한 여유가 있다면 GC가 수행된다 하더라도 수거되지 않는다. 하지만 메모리가 부족하면 수거될 확률이 높다.

</br >

## 코드 예제

```java
class Data {
    private int[] array = new int[2000];
}

public class ReferenceTest {

    private List<WeakReference<Data>> weakRefs = new LinkedList<>();
    private List<SoftReference<Data>> softRefs = new LinkedList<>();
    private List<Data> strongRefs = new LinkedList<>();

    public void weakReferenceTest() {
        try {
            while (true) {
                weakRefs.add(new WeakReference<Data>(new Data()));
            }
        } catch (OutOfMemoryError ofm) { // weak일 경우 out of memory 발생 하지 않는다.
            System.out.println("out of memory!");
        }
    }

    public void softReferenceTest() {
        try {
            while (true) {
                softRefs.add(new SoftReference<Data>(new Data()));
            }
        } catch (OutOfMemoryError ofm) { // weak일 경우 out of memory 발생 하지 않는다.
            System.out.println("out of memory!");
        }
    }


    public void strongReferenceTest() {
        try {
            while (true) {
                strongRefs.add(new Data());
            }
        } catch (OutOfMemoryError ofm) { // Strong일 경우 out of memory 발생
            System.out.println("out of memory!");
        }
    }


    public static void main(String[] args) {
        System.out.println("실행");

        ReferenceTest test = new ReferenceTest();
//        test.weakReferenceTest();
//        test.softReferenceTest();
        test.strongReferenceTest();

        System.out.println("종료");
    }
}
```

위 코드를 돌려보면 WeakReference와 SoftReference는 GC에서 Memory를 수거하여 사용하기 때문에 종료가 되지 않는다.

하지만 StrongReference(new 연산자)는 out of memory가 발생한다.

</br >

## 참조 해제 코드 예제

```java
public class ReferenceTest {

    public static void main(String[] args) {
        Fruit apple = new Fruit("사과");
        Fruit banana = new Fruit("바나나");

        Fruit strong = apple;
        WeakReference<Fruit> weak = new WeakReference(banana);

        apple = null;
        banana = null;

        //GC 실행
        System.gc();
        
        System.out.println(apple == null ? "null" : apple.name);
        System.out.println(banana == null ? "null" : banana.name);
        
        System.out.println(strong == null ? "null" : strong.name);
        System.out.println(weak == null ? "null" : weak.get());
    }

    private static class Fruit {
        private String name;

        public Fruit(String name) {
            this.name = name;
        }
    }
}
```

![img](https://blog.kakaocdn.net/dn/sn0Se/btq66kFadru/gLofketUMc5H0KuT6qVFzk/img.png)

위 코드의 결과로 참조가 해제된 apple, banana 객체는 null을 출력한다.

apple을 참조하고 있던 strong 객체는 apple의 참조가 해제되었음에도 불구하고 사과를 출력한다. 하지만 banana를 참조하고 있던 weak 객체는 banana의 참조가 해제되니 null을 출력하는걸 볼 수 있다.

즉, strong은 강함 참조이기 때문에 원본에 null을 넣어 참조를 해제해줘도, strong이 참조하고 있으므로 객체가 해제되지 않았다.

반면에 weak는 약한 참조이기 때문에 원본에 null을 넣어 참조를 해제해주니 weak가 참조하고 있어도 객체가 해제되었다.

