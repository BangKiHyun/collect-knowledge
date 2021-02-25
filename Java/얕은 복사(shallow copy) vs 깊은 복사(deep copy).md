# 얕은 복사(shallow copy) vs 깊은 복사(deep copy)

개발을 하다가 얕은 복사와 깊은 복사라는 종종 들어봤다. 얕은 복사와 깊은 복사가 무엇인지 정확히 짚고 넘어가기 위해 두 개념을 공부해 보기로했다.

## 얕은 복사(call by reference)

### 얕은 복사란?

- 얕은 복사란 **객체의 참조값(주소값)을 복사**하는 것이다.
- 복사된 객체는 원본 객체와 같은 주소값을 참조한다.
- 그렇기 때문에, **복사된 객체의 값이 바뀌면 원본 객체의 값 또한 변경**된다.

## 깊은 복사(call by value)

### 깊은 복사란?

- 깊은 복사란 객체의 **실체값을 복사**하는 것이다.
- **복사될 객체에 새 주소를 할당**하여 원본 객체의 모든 값을 복사한다.
- 주소값을 참조하지 않기 때문에 **복사된 객체의 변화가 원본 객체에게 영향이 미치지 않는다.**

</br >

## 얕은 복사 코드 예제

```java
import java.util.ArrayList;

public class ShallowCopy {

    public static void main(String[] args) {
        ArrayList<String> originObject = new ArrayList<>();
        originObject.add("apple");
        originObject.add("banana");
        originObject.add("grape");

        //얕은 복사 수행!
        ArrayList<String> shallowCopyObject = originObject;
        printObject(originObject, shallowCopyObject);

        //복사된 객체에 strawberry 값 추가!!
        System.out.println("복사된 객체에 값 strawberry 값 추가 후");
        System.out.println("-------------------------------");
        shallowCopyObject.add("strawberry");
        printObject(originObject, shallowCopyObject);

        // 동일성 검사
        System.out.println("동일성 검사 결과");
        System.out.println(originObject == shallowCopyObject);
    }

    private static void printObject(ArrayList<String> originObject, ArrayList<String> copyObject) {
        System.out.println("원본 객체 값 출력!");
        System.out.println(originObject.toString());
        System.out.println();

        System.out.println("얕은 복사된 객체 값 출력!");
        System.out.println(copyObject.toString());
        System.out.println();
    }
}
```

![image](https://user-images.githubusercontent.com/43977617/109142800-a87b6b00-77a2-11eb-8efe-935edf8033c9.png)

**shallowCopyObject에만 값을 추가하였는데 originOject에도 값이 추가**된 것을 볼 수 있다.

**동일성 검사 결과 또한 true를 반환**하는 것으로 보아 주소 값이 일치한다는 것을 알 수 있다.

</br >

## 깊은 복사 코드 예제

~~~java
import java.util.ArrayList;

public class DeepCopy {

    public static void main(String[] args) {
        ArrayList<String> originObject = new ArrayList<>();
        originObject.add("apple");
        originObject.add("banana");
        originObject.add("grape");

        //깊은 복사 수행!
        ArrayList<String> deepCopyObject = new ArrayList<>(originObject);
        printObject(originObject, deepCopyObject);

        //복사된 객체에 strawberry 값 추가!!
        System.out.println("복사된 객체에 값 strawberry 값 추가 후");
        System.out.println("-------------------------------");
        deepCopyObject.add("strawberry");
        printObject(originObject, deepCopyObject);

        // 동일성 검사
        System.out.println("동일성 검사 결과");
        System.out.println(originObject == deepCopyObject);
    }

    private static void printObject(ArrayList<String> originObject, ArrayList<String> copyObject) {
        System.out.println("원본 객체 값 출력!");
        System.out.println(originObject.toString());
        System.out.println();

        System.out.println("깊은 복사된 객체 값 출력!");
        System.out.println(copyObject.toString());
        System.out.println();
    }
~~~

![img](https://blog.kakaocdn.net/dn/cwPMlZ/btqYurfl7KP/hFMDqaWr9wphjIBryPM2Mk/img.png)

**deepCopyObject에 추가한 값이 originOject에는 영향을 미치지 않은 것을** 볼 수 있다.

**동일성 검사 결과 또한 false를 반환**하는 것으로 보아 주소값이 일치하지 않는다는 것을 알 수 있다.

</br >

## ArrayList의 Item 으로 객체(Object)가 선언되어 있을 때

만약에 ArrayList의 Item으로 Object가 선언되어 있으면 어떻게 될까? 깊은 복사가 잘 이뤄질까?? 확인해 보자.

```java
class Fruit{
    private String name;
    private String color;

    public Fruit(String name, String color){
        this.name = name;
        this.color = color;
    }

    public String getName() {
        return name;
    }

    public String getColor() {
        return color;
    }

    @Override
    public String toString() {
        return "Fruit{" +
                "name='" + name + '\'' +
                ", color='" + color + '\'' +
                '}';
    }
}
```

~~~java
import java.util.ArrayList;

public class ArrayListDeepCopy {
    public static void main(String[] args) {
        ArrayList<Fruit> originObject = new ArrayList<>();
        originObject.add(new Fruit("apple", "red"));
        originObject.add(new Fruit("banana", "yellow"));
        originObject.add(new Fruit("grape", "purple"));

        //깊은 복사 수행!
        ArrayList<Fruit> deepCopyObject = new ArrayList<>(originObject);
        printObject(originObject, deepCopyObject);

        //복사된 객체에 strawberry 값 추가!!
        System.out.println("복사된 객체에 값 strawberry 값 추가 후");
        System.out.println("-------------------------------");
        deepCopyObject.add(new Fruit("strawberry", "red"));
        printObject(originObject, deepCopyObject);

        // 동일성 검사
        System.out.println("동일성 검사 결과");
        System.out.println(originObject == deepCopyObject);
    }

    private static void printObject(ArrayList<Fruit> originObject, ArrayList<Fruit> copyObject) {
        System.out.println("원본 객체 값 출력!");
        System.out.println(originObject);
        System.out.println();

        System.out.println("깊은 복사된 객체 값 출력!");
        System.out.println(copyObject.toString());
        System.out.println();
    }
}
~~~

![img](https://blog.kakaocdn.net/dn/bsDlHb/btqYAsYyEwp/SWLmbSCJF5eBKm4sKZxgI1/img.png)

Fruit이라는 클래스를 선언하고 ArraysList 안에 Fruit이라는 클래스를 저장시키도록 구현했다.

위의 결과만 봐서는 깊은 복사가 제대로 이뤄진 것 같다.

그렇다면 List에 선언한 Fruit 객체까지 깊은 복사가 이뤄졌을까요? 확인해보자.

```java
import java.util.ArrayList;

public class ArrayListDeepCopy {
    public static void main(String[] args) {
        ArrayList<Fruit> originObject = new ArrayList<>();
        originObject.add(new Fruit("apple", "red"));
        originObject.add(new Fruit("banana", "yellow"));
        originObject.add(new Fruit("grape", "purple"));

        //깊은 복사 수행!
        ArrayList<Fruit> deepCopyObject = new ArrayList<>(originObject);

        //복사된 객체의 Fruit 객체 값 변경!!
        System.out.println("복사된 객체의 Fruit 객체 값 변경 후");
        System.out.println("-------------------------------");
        final Fruit fruit = deepCopyObject.get(0);
        fruit.changeName("cherry");
        printObject(originObject, deepCopyObject);
    }

    private static void printObject(ArrayList<Fruit> originObject, ArrayList<Fruit> copyObject) {
        System.out.println("원본 객체 값 출력!");
        System.out.println(originObject);
        System.out.println();

        System.out.println("깊은 복사된 객체 값 출력!");
        System.out.println(copyObject.toString());
        System.out.println();
    }
}
```

![img](https://blog.kakaocdn.net/dn/OZxp9/btqYwgEtnV3/2iKbmJnJkKSwgMR7Wvy4B1/img.png)

복사된 객체의 Fruit 값을 apple -> cherry로 변경했다.

결과를 보면 **원본 객체의 apple 값까지 cherry로 변경된 것을 확인할 수 있다.**

**ArrayList 자체의 주소 값은 다르지만 Item으로 선언한 객체까지는 깊은 복사를 수행하지 못한 것 같다.**

해결 방법으로는 해당 객체(Fruit)에 대한 깊은 복사를 수행해주면 된다.

```java
...
for(Fruit fruit : origin){
    copy.add(new Fruit(fruit));
}
```

