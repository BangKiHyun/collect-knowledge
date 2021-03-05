# hashCode()와 equals()

## 해시코드(hashCode)

### 해시 코드란?

자바에서 동일한 이름의 객체가 여러 개 생성될 수 있는데, 이 객체들은 이름만 같을뿐 서로 다른 상태를 가진 객체들입니다. 이 객체들을 구분하기 위해 고유한 정수값으로 출력시켜주는 메서드가 바로 hashCoed()입니다.

즉, **해시 코드란 객체를 식별하는 고유한 정수값을 의미**합니다.

해시 코드는 일반적으로 **객체의 내부 주소를 정수값으로 변환하는 형태로 구현**됩니다.

### 코드 예제

```java
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

```java
public class HashcodeTest {

    public static void main(String[] args) {
        User firstUser = new User("Bang", 26);
        System.out.println("첫 번째 User의 참조변수 값: " + firstUser);
        System.out.println("첫 번째 User의 hashcode 값: " + firstUser.hashCode() + "\n");

        User secondUser = new User("Bang", 26);
        System.out.println("두 번째 User의 참조변수 값: " + secondUser);
        System.out.println("두 번째 User의 hashcode 값: " + secondUser.hashCode() + "\n");
    }
}
```

![img](https://blog.kakaocdn.net/dn/bb53Pj/btqZheUfht9/P0Kq9lwjD0Yhs9GkjWJuS1/img.png)

- 첫줄 7229724f은 16진수이고, 이를 10진수로 변환시킨 1915318863가 hashcode 리턴값입니다.
- 서로다른 객체는 서로 다른 주소값을 갖기 때문에 다른 hashcode 값을 갖는걸 볼 수 있습니다.

</br >

## Hashcode 메서드의 재정의

hashCode() 메서드는 Object 클래스에서 정의한 그대로의 메서드입니다. 즉, 주소값과 연관이 있습니다.

위의 User 클래스를 정의할 때 hashcode 메서드 또한 재정의하여 변경하지 않았기 때문에 Object 클래스에서 정의한 그대로의 메서드입니다. 그러므로 주소값과 연관이 있습니다.

하지만 String의 경우 hashcode가 주소값과 관련이 없습니다. String 클래스의 hashcode는 오버라이드를 통해 재정의됐기 때문입니다.

### 코드 예제

```java
public class StrHashcodeTest {
    public static void main(String[] args) {
        String name1 = "bang";
        String name2 = "bang";

        System.out.println("name1의 hashcode 값: " + name1.hashCode());
        System.out.println("name2의 hashcode 값: " + name2.hashCode());
    }
}
```

![img](https://blog.kakaocdn.net/dn/UVFqA/btqZoxY9MXW/AEq4QXg3ntu5rYPAK6uFw1/img.png)

출력 결과로 보았을 때, String 클래스 입장에서는 name1과 name2는 같은 객체입니다. String클래스는 Object클래스의 hashCode() 메서드를 그대로 사용하지 않고 재정의하였기 때문입니다.

즉, String클래스 안에서 hashCode() 메서드를 오버라이딩하여 같은 String객체에 대해 hashcode가 같아지도록 만들어 준 것입니다.

</br >

## hashCode() 메서드를 재정의 필요성

HashSet, HashMap, HashTable은 **해시코드가 다른 값이라면 동등성 비교(equals)를 시도조차 하지 않도록 최적화되어있습니다.**

그렇기 때문에, 동등성 비교를 위해 hashCode() 메서드의 재정의 필요성을 느낄 수 있습니다.

### 코드 예제

```java
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
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

```java
public class HashSetExam {

    public static void main(String[] args) {
        Set<User> set = new HashSet<>();
        set.add(new User("bang", 26));
        set.add(new User("bang", 26));

        System.out.println("hashCode() 재정의 전 hashSet 크기: " + set.size());
    }
}

```

![img](https://blog.kakaocdn.net/dn/c0i1p7/btqZiUH7KU5/5CPaSpKR02ecSbNs1Y5tzK/img.png)

User 클래스의 euqals() 메서드만 재정의하고, hashCode() 메서드는 재정의하지 않았습니다.

출력 결과를 보면 같은 User의 상태가 같고 equals() 메서드를 재정의 하였지만 hashCode의 값이 다르기 때문에 HashSet의 크기가 2로 나온 것을 볼 수 있습니다.

그럼 User 클래스의 hashCode() 메서드를 재정의하고 다시 한번 확인해 보겠습니다.

~~~java
public class User {

    //...

    @Override
    public int hashCode() {
        return age + name.hashCode();
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
~~~

![img](https://blog.kakaocdn.net/dn/lhQLI/btqZn1lXXpO/jCve4fda4G0T46wEZKoLrK/img.png)

hashCode() 메서드를 위와 같이 재정의한 후 출력 결과 hashSet의 크기가 2로 나온 것을 확인할 수 있습니다.

</br >

## hashCode() 메서드와 equals() 메서드 관계

Object 명세 규약을 보면 다음과 같은 말이 있습니다.

1. equals 비교에서 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode메서드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다. 단, 애플리케이션을 다시 실행한다면 이 값이 달라져도 상관없다.
2. equals(Object)가 두 객체가 같다고 판단했다면, hashCode는 똑같은 값을 반환해야 한다.

위 명세 규약 중 2번째 규약을 보면 **equals() 메서드로 두 객체가 같다고 판단되면, 해시코드는 똑같은 값을 반환**해야 한다는 말이 있습니다.

만약 equals()만 재정의한 후, hashCode() 메서드를 재정의 하지 않으면 동치 관계인 두 객체가 서로 다른 해시코드를 반환하게 됨으로 2번째 규약을 어기게 됩니다.

euqals()를 재정의할 때, hashCode() 또한 재정의 해줍시다.