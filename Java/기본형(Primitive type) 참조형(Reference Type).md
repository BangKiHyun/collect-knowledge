# 기본형(Primitive Type) 참조형(Reference Type)

## 기본형과 참조형 종류

```
Java Data Type 
ㄴ Primitive Type
    ㄴ Boolean Type(boolean)
    ㄴ Numeric Type
        ㄴ Integral Type
            ㄴ Integer Type(short, int, long)
            ㄴ Floating Point Type(float, double)
        ㄴ Character Type(char)
ㄴ Reference Type
    ㄴ Class Type
    ㄴ Interface Type
    ㄴ Array Type
    ㄴ Enum Type
    ㄴ etc.
```

</br >

## 기본형(Primitive Type)

### 기본형이란?

- 기본형은 실제 값을 저장하는 공간입니다.
- 자바에서 기본 자료형은 반드시 사용하기 전에 선언되어야 합니다.
- 비객체 타입으로 null값을 가질 수 없습니다.

### 기본형의 종류(8가지)

```
Type        Bits      Range of Values
----------------------------------------------------------------------------------------
byte         8bits    -2^7 ~ 2^7-1 (-128 ~ 127)
short       16bits    -2^15 ~ 2^15-1 (-32768 ~ 32767)
int         32bits    -2^31 ~ 2^31-1 (-2147483648 ~ 2147483647)
long        64bits    -2^63 ~ 2^63-1 (-9223372036854775808 ~ 9223372036854775807)
float       32bits    0x0.000002P-126f ~ 0x1.fffffeP+127f
double      64bits    0x0.0000000000001P-1022 ~ 0x1.fffffffffffffP+1023  
char        16bits    \u0000 ~ \uffff (0 ~ 2^15-1) * 자바에서 unsgined로 동작하는 자료형
boolean      1bit     true, false
```

</br >

## 참조형(Reference Type)

### 참조형이란?

- 참조형은 **값이 저장된 곳의 주소를 저장하는 공간**으로 객체의 주소를 저장합니다.
- 선언한 자료형이 기본형이 아닌 경우 참조형입니다.
- 참조형에는 클래스형, 인터페이스 형, 배열형이 있습니다.


### 1. Class Type

클래스형은 기본형과 다르게 **객체를 참조**하는 형태입니다.

### 코드예제

```java
public class MyObject {
    private int number;

    public MyObject(int number) {
        this.number = number;
    }

    public int getNumber() {
        return number;
    }

    public void setNumber(int number) {
        this.number = number;
    }
}

public class ClassTypeExam {

    public static void main(String[] args) {
        MyObject firstClassType = new MyObject(2);
        MyObject secondClassType = new MyObject(4);
        System.out.println(String.format("First Class Type number: %d", firstClassType.getNumber()));
        System.out.println(String.format("Second Class Type number: %d", secondClassType.getNumber()));

        firstClassType = secondClassType;
        System.out.println("-----------------------------------------");
        System.out.println("First Class Type에 Second Class Type 대입 후");
        System.out.println(String.format("First Class Type number: %d", firstClassType.getNumber()));

        secondClassType.setNumber(6);
        System.out.println("-----------------------------------------");
        System.out.println("Second Class Type 값 변경 후");
        System.out.println(String.format("First Class Type number: %d", firstClassType.getNumber()));
    }
}
```

![img](https://blog.kakaocdn.net/dn/b3igKa/btqYLylkXUE/HKsDLiSTKYf5liKqhjzqY0/img.png)

출력 결과를 보면 첫 번째 객체의 멤버 변수 값을 바꿨지만, 두 번째 객체도 같은 객체를 참조하기 때문에 동일한 값을 출력하는 것을 볼 수 있습니다.

**위 코드에서 firstClassType변수와 secondClassType변수가 가지는 것은 실제 객체가 아닌 객체의 주소를 가진다고 생각하면 됩니다.**  첫 번째 객체와 두 번째 객체는 같은 객체의 주소를 가지고 있기 때문에 어느 한쪽이 변하더라도 값이 동일한 것입니다.

두 객체의 연결고리를 끊고 싶다면 어느 하나의 변수에 `null`이나 `new MyObject()`를 통해 객체의 주소를 지워버리거나 새로운 객체를 가리키게 하면 됩니다.

</br >

### 2. Wrapper Class

- 래퍼 클래스는 기본형을 클래스로 감싼 형태입니다.
- 기본형은 비객체이기 때문에 null을 넣을 수 없습니다. 하지만 래퍼 클래스를 활용하면 null 값을 넣는것이 가능합니다.

~~~java
public class WrapperClassExam {
    public static void main(String[] args) {
        Integer wrpperForInt = 30;
        Integer wrapperInNull = null;
    }
}
~~~

</br >

### 3. Interface Type

인터페이스를 만들게 되면 새로운 참조 자료형을 만드는 것과 같습니다.

</br >

### 4. Array Type

배열형은 기본형으로도 만들 수 있고 참조형으로도 만들 수 있습니다.

```java
public class ArrayType {
    public static void main(String[] args) {
        int [] arrayTypeForInt = new int[2];
        Long [] arrayTypeForLong = new Long[2];
        Object[][] arrayTypeForObject = null;
    }
}
```

- 자료형에 대해 []를 선언함으로 배열을 지정할 수 있습니다.
- 참고로 배열형 변수 또한 배열의 주소를 가지고 있는 것이기 때문에 클래스형의 특징과 일치합니다.
- 같은 객체의 주소를 바라보게 만들면 동일한 배열을 가리키게 됩니다