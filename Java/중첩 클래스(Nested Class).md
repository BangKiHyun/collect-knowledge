# 중첩 클래스(Nested Class)

## 정의

- 하나의 클래스 내부에 또 다른 클래스가 내포되어 있는 상태

</br >

## 종류

중첩 클래스는 크게 정적 및 비 정적 클래스 두 가지로 나뉜다.

static으로 선언 된 중첩 클래스를 정적 클래스, 정적이 아닌 중첩 클래스를 비 정적 클래스라고 한다.

1. 비 정적 클래스(Non-Static Nested Class)
   - 내부 클래스(Inner class)
   - 지역 클래스(Local class)
   - 익명 클래스(Anonymous class)
2. 정적 클래스(Static Nested Class)

</br >

## 내부 클래스(Inner class)

- 일반 클래스 내부에 생성된다.
- 밖에 있는 클래스는 내부 클래스의 멤버변수처럼 사용할 수 있다.
  - 사용하려면 new로 인스턴스 생성
- 내부 클래스는 자신의 밖에 있는 클래스의 자원을 직접 사용할 수 있다.

### 형식

~~~java
public class Outer {
  //...

    public class Inner {
      	//...
        }
    }
}
~~~

### 객체 생성

~~~java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
~~~

</br >

## 지역 클래스(Method Local Inner Class)

- 메서드 내부에 클래스를 정의한다.
- 메서드 내의 지역변수처럼 쓰인다.
- 메서드 내부에서 new로 인스턴스를 생성해야 한다.
- 메서드 밖에서 사용할 수 없다.

### 형식

~~~java
public class Outer {
  	//...

    public void Method {
      		//...
      		class Inner {
          //...
        	}
        }
    }
}
~~~

</br >

## 익명 클래스(Anonymous Class)

- 익명 클래스는 인스턴스 이름이 없다.
  - new로 인스턴스를 생성함과 동시에 부모 클래스를 상속받아 내부에서 오버라이딩해서 사용한다.
- 매개변수로 사용할 수 있다.
- 익명 클래스 내부 변수나 메서드는 익명 클래스 밖에서 사용할 수 없다.

### 형식

~~~java
class Inner {
		//...
}

class Outer {
		//...
    public void Method() {
    		//...
        new Inner() {
            @Override
        }
    }
}
~~~

</ br>

## 정적 클래스(Static Nested Class)

- 내부에 static으로 선언한 클래스
- 외부 클래스에서 static이 붙은 메서드나 변수를 사용할 수 있음
- Outer클래스의 객체가 없어도 Inner 클래스의 객체 생성이 가능.

### 형식

~~~java
public class Outer {
  //...

    public static class Inner {
      	//...
        }
    }
}
~~~

