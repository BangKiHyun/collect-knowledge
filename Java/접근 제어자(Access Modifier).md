# 접근 제어자(Access Modifier)

자바에서 접근 제어자는 **정보 은닉**을 위해 필수적으로 알고 있어야 할 내용이다.

## 접근 제어자란?

- 접근 제어자는 변수, 메서드, 클래스 선언 시 사용되며 해당 변수, 메서드, 클래스의 접근 권한을 제어하는 역할한다.
- 즉, 접근 제어자를 통해 해당 정보를 외부로부터 보호해주기 때문에, 정보 은닉을 구현할 수 있게 도와줍니다.

</br >

## 접근 제어자 종류

접근 제어자의 종류로는 4가지가 있다. 

- private: 멤버를 선언한 톱레벨 클래스(해당 클래스)에서만 접근 가능
- package-private(default): 멤버가 소속된 패키지 안의 모든 클래스에서 접근 가능
- protected: package-private의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근 가능
- public: 모든 곳에서 접근 가능 (공개 API)

접근 범위가 좁은 순서대로 알아보자.

1. ### private

   - 접근 범위가 가장 좁은 제한자이다.
     
   - 자기 자신의 클래스 내에서만 접근이 가능하다.
     
   - 한마디로 private가 붙은 변수, 메소드는 **해당 클래스에서만 접근이 가능하다.**
     
   ```java
     public class PrivateTset {
         private String name;
     
         private String getName() {
             return this.name;
         }
     }
     ```
     
     위 예제의 name 변수와 getName 메소드는 PrivateTest 클래스에서만 접근이 가능하다.
     
     

2. ### package-private(default)

   - package-private은 접근 제한자를 명시하지 않았을 경우를 의미한다. 그래서 default 제한자라고 부른다.

   - **동일한 패키지**에 있는 모든 클래스에서 접근이 가능하다.

   - DefaultTest.java

     ```java
     package javajava.test;
     
     public class DefaultTest {
         String name = "Bang";
     }
     ```

   - MyName.java

     ```java
     package javajava.test;
     
     public class MyName{
         String name = "Oh";
     
         public static void main(String[] args) {
             DefaultTest test = new DefaultTest();
             System.out.println(test.name);
         }
     }
     ```

   - Default와 MyName의 패키지는 javajava.test로 동일하다.

     - 접근제어자가 default이므로 MyName 클래스에서 test.name 으로 DefaultTest의 name 변수에 접근이 가능하다

3. ### protected

   - 동일한 패키지내의 클래스 또는 **해당 클래스를 상속받은 외부 패키지의 클래스**에서 접근이 가능하다.
     
   -  ProtectedTest.java

     ```java
     package javajava.test;
     
     public class ProtectedTest {
         protected String name = "Bang";
     }
     ```

   - MyName.java

     ```java
     package javajava.test.protect;
     
     import test.ProtectedTest;
     
     public class MyName extends ProtectedTest{
         public static void main(String[] args) {
             ProtectedTest test = new ProtectedTest();
             System.out.println(test.name);
         }
     }
     ```

     ProtectedTest클래스를 상속받은 MyName 클래스는 ProtectedTest클래스와 패키지가 다르지만 protected로 설정된 name 변수를 사용할 수 있다.</br >

     만약 name의 접근제어자가 default 였다면 에러를 유발할 것이다


4. ### public

   - 접근에 제한이 없다.
   - 즉, public 접근 제어자로 되어이쓰면 어떤 클래스에서도 접근이 가능하다
   - Public 접근 제어자가 많다면 그만큼 공개되는 정보들이 많기 때문에 최소화를 하는게 좋다.

정보 은닉의 기본 원칙으로 **모든 클래스와 멤버의 접근성을 가능한 좁혀야 한다**는 말이 있다.

접근성을 좁혀야 한다는 건 접근 제어자와 밀접한 관련이 있습니다. 외부에 노출할 필요가 없는 정보들은 최대한 은닉하기 위해 접근 권한을 최소화하도록 습관을 들여야겠다.