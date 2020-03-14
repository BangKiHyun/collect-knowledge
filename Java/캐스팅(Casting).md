## 캐스팅(Casting)

- 캐스팅이란 타입을 변환하는 것을 말하면 형변환이라고도 한다
  자바의 상속 관계에 있는 부모와 자식 클래스 간에는 서로 간의 형변환이 가능하다



## 업캐스팅 다운캐스팅

- 자식 클래스가 부모 클래스의 타입으로 캐스팅 되는 것
  다른말로, 서브 클래스의 객체가 수퍼 클래스 타입으로 형변환되는 것을 말한다

- 코드 예제

  ```java
  // 캐스팅 후 멤버에 직접 접근 확인을 위해
  // private 선언과 getter 메서드는 생략합니다.
  class Person {
      String name;
  
      public Person(String name) {
          this.name = name;
      }
  }
  
  class Student extends Person {
      String dept;
  
      public Student(String name) {
          super(name);
      }
  }
  
  public class CastingTest {
      public static void main(String[] args) {
          // 레퍼런스 student를 이용하면 name, dept에 접근 가능
          Student student = new Student("MadPlay");
  
          // 레퍼런스 person을 이용하면 Student 객체의 멤버 중
          // 오직 Person 클래스의 멤버만 접근이 가능합니다.
          Person person = student;
          person.name = "Kimtaeng";
          
          // 아래 문장은 컴파일 타임 오류
          person.dept = "Computer Eng";
      }
  }
  ```

 위 코드에서 person 레퍼런스 변수는 Student 객체를 가리키고 있으면
 Person 타입이기 때문에 오로지 자신의 클레스에 속한 멤버만 접근 가능하다
 따라서 dept 멤버는 Student 타입의 멤버이므로 컴파일 시점에 오류가 발생한다

 이때 sutdent 객체는 person 타입으로 자동 형변환이 된다. 이것이 업캐스팅 이다

```java
// 업캐스팅 자동 타입 변환
Person person = student;

// 아래와 같이 명시적으로 타입 캐스팅 선언을 하지 않아도 된다.
Person person = (Person) student;
```

업캐스팅을 사용하는 이유는 다형성과 관련이 있다.



- 다운 캐스팅은 업캐스팅을 반대로 하는 것으로
  고유한 특성을 잃은 서브 클래스의 객체를 다시 복구시켜주는 것을 말한다
  타입을 명시적으로 선언해야 한다.

  ```java
          // 다운캐스팅
          Student student = (Student) person;
  ```