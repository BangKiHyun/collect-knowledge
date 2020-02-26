### 기본형(Primitive type) 참조형(Reference Type)

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

- Primitive type

  - 자바에서 기본 자료형은 반드시 사용하기 전에 선언되어야 한다.

  - 비객체 타입으로 null값을 가질 수 없다.

  - 기본 자료형의 종류

  - ```
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

- Reference type

  - 선언한 자료형이 기본형이 아닌 경우 참조형이다

  - 참조형에는 클래스형, 인터페이스 형, 배열형이 있다

    1. Class type

       클래스형은 기본형과 다르게 객체를 참조하는 형태

       ex)

       ```java
       class MyObject{
           private int index;
           MyObject(int index) {
               this.index = index;
           }
           public int getIndex() {
               return index;
           }
           public void setIndex(int index) {
               this.index = index;
           }
       }
       public class ClassType {
           public static void main(String[] args) {
               MyObject a = new MyObject(2);
               MyObject b = new MyObject(4);
               System.out.println(a.getIndex()); // "a" result is 2.
               a = b;
               System.out.println(a.getIndex()); // "a" result is 4.
               b.setIndex(6);
               System.out.println(a.getIndex()); // "a" result is 6.
           }
       }
       ```

       b 객체의 멤버 변수 값을 바꿨지만 a 객체도 같은 객체를 참조하기 때문에 동일한 값을 출력하는 것을 볼 수 있습니다.

       **위 코드에서 a와 b라는 변수가 가지는 것은 실제 객체가 아닌 객체의 주소를 가진다고 생각하면 됩니다.**  a와 b는 같은 객체의 주소를 가지고 있기 때문에 어느 한쪽이 변하더라도 값이 동일한 것입니다.

        a와 b의 연결고리를 끊고 싶다면 어느 하나의 변수에 null 이나 new MyObject()를 통해 객체의 주소를 지워버리거나 새로운 객체를 가리키게 하면 됩니다.

    2. Wrapper Class

       기본형은 비객체이기 때문에 null을 넣을 수 없다. 하지만 래퍼 클래스를 활용하면 null값을 넣는것도 가능하다.

       래퍼 클래스는 기본형을 클래스로 감싼 형태이다.

    3. Interface Type

       인터페이스를 만들게 되면 새로운 참조 자료형을 만드는 것과 같다

    4. Array type

       배열형은 기본형으로도 만들 수 있고 참조형으로도 만들 수 있다.

       ```java
       public class ArrayType {
           public static void main(String[] args) {
               int [] i = new int[2];
               Long [] l = new Long[2];
               Object[][] o = null;
           }
       }
       ```

       자료형에 대해 []를 선언함으로 배열을 지정할 수 있습니다. 참고로 배열형 변수 또한 배열의 주소를 가지고 있는 것이기 때문에 클래스형의 특징과 일치합니다. 같은 객체의 주소를 바라보게 만들면 동일한 배열을 가리키게 됩니다