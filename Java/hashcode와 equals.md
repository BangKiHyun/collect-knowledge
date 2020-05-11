## hashcode와 equals

#### hashcode란?

> 객체의 해시 코드 값을 반환합니다. 이 메소드는 HashMap에서 제공하는 것과 같은 해시 테이블의 이점을 위해 지원됩니다.

- HashTable, HashMap은 hashcode를 이용함으로써, 객체를 저장하는 다른 도구들(ArrayList)에 비해 어떠한 장점을 갖게 된다. 고 볼 수 있다.
  즉, Key의 hashcode를 통해 Value값을 더 쉽게 찾아낼 수 있다.

- 코드를 통해 확인해보면 아래와 같다.

User.class

```
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

hashcodeTest.class

```
public class HashcodeTest {
    public static void main(String[] args) {
        Object object = new Object();
        System.out.println("object의 참조변수 값: " + object);
        System.out.println("object의 hashcode 값: " + object.hashCode() + "\n");

        User user = new User("Bang", 26);
        System.out.println("user의 참조변수 값: " + user);
        System.out.println("user의 hashcode 값: " + user.hashCode() + "\n");

        String str = "StrBang";
        System.out.println("String 객체의 hashcode 값 : " + str.hashCode());
    }
}
```

위와 같이 작성하고 출력하면 결과는 아래와 같다.

~~~
object의 참조변수 값: java.lang.Object@72ea2f77
object의 hashcode 값: 1927950199

user의 참조변수 값: hashAndEql.User@816f27d
user의 hashcode 값: 135721597

String 객체의 hashcode 값 : -218279959
~~~

- 첫줄 72ea2f77은 16진수이고, 이를 10진수로 변환시킨 1927950199가 hashcode 리턴값 이다.
- 서로다른 객체는 서로 다른 주소값을 갖기 떄문에 다른 hashcode 값은 갖는다.
- 한마디로, hashcode()메소드는 **각 객체에 대응되는 고유한 정수값을 리턴**한다,



#### Hashcode 메서드의 재정의

- 원래 hashcode 메서드는 Object 클래스에서 정의한 그대로의 메서드이다.
  즉, **주소값과 연관**이 있다.
- 위의 User 클래스를 정의할 때 hashcode 메서드 또한 **오버라이드하여 변경하지 않았기 때문에 Object 클래스에서 정의된 그대로의 메서드** 이다. 그러므로 주소값과 연관이 있다.
- 하지만 **String의 경우는 hashcode가 주소값과 관련이 없다**.
  String 클래스의 hashcode는 **오버라이드 과정에서 새롭게 정의**된다.

```
public class StrHashcodeTest {
    public static void main(String[] args) {
        String name1 = "bang";
        String name2 = "bang";

        System.out.println("name1의 hashcode 값: " + name1.hashCode());
        System.out.println("name2의 hashcode 값: " + name2.hashCode());
    }
}
```

위와 같이 작성하고 출력하면 결과는 아래와 같다.

**name1의 hashcode 값: 3016248**
**name2의 hashcode 값: 3016248**

- 위 결과로 보았을 때, String 클래스 입장에서는 name1 과 name2 는 같은 객체이다.
  String클래스는 Object클래스의 hashcode 메서드를 그대로 사용하지 않고 재정의 하였다.
- 즉, **String클래스 안에서 hashcode 메서드를 오버라이딩하여 같은 String 객체에 대해(equals 리턴값 true) hashcode가 같아지도록 만들어 준 것이**다.
- hashcode를 활용해서 Map이나 Set에 저장된 Key값을 찾아야 할 때,
  같은 객체임에도 불구하고 hashcode값이 다르면 제대로 찾을 수가 없다.
- 위의 문제를 해결하기 위해서 각각의 객체에 대해(equals 리턴값 true) hashcode가 같아지도록
  Hashcode 메서드를 재정의 해줘야 한다.

```
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof User){
            User user = (User) obj;
            return (age == user.age) && (name.equals(user.name));
        } else{
            return false;
        }
    }
}
```

```
public class OverridingHashTest {
    public static void main(String[] args) {
        User user1 = new User("bang", 26);
        User user2 = new User("bang", 26);

        System.out.println("user1.equals(user2) 는 " + user1.equals(user2) + " 입니다.");

        System.out.println("user1 의 hashcode 값: " + user1.hashCode());
        System.out.println("user2 의 hashcode 값: " + user2.hashCode());
    }
}
```

위와 같이 equals 메서드를 재정의 하고 출력하면 결과는 다음과 같다.

~~~
user1.equals(user2) 는 true 입니다.
user1 의 hashcode 값: 1418481495
user2 의 hashcode 값: 303563356
~~~

- 위 코드를 보면  equals 메서드만 수정해도 user1.equals(user2) 를 하였을때 true가 나온다.
  하지만, 두 객체의 hashcode가 다르게 나온다.
- 하지만 이것은 hashcode의 의미를 해치고 있다고 볼 수 있다.
  User클래스에서 위 두 객체는 같은 객체이므로, hashcode가 같아야만 hashcode가 의미를 갖는것이다.
  그래서 다음과 같이 hashcode 메서드 또한 재정의해 주었다.

```
@Override
public int hashCode() {
    return age + name.hashCode();
}
```

출력 결과는 다음과 같다.

```
user1.equals(user2) 는 true 입니다.
user1 의 hashcode 값: 3016274
user2 의 hashcode 값: 3016274
```

정리하자면 **equals 메서드에 의해 true가 나오는 두 객체의 hashcode는 같아야 한다.**
그래야 hashcode가 의미가 있는 것이다.

