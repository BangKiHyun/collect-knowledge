# Java Virtual Machine(JVM)

## JVM이란?

운영체제 위에서 동작하는 프로세스로 **자바 코드를 컴파일해서 얻은 바이트 코드를 해당 운영체제가 이해할 수 있는 기계어로 바꿔 실행시켜주는 역할을 합니다.**

- 자바 가상머신으로 자바와 운영체제 사이에서 중계자 역할을 합니다.
- 자바가 운영체제 종류에 영향받지 않고 돌아갈 수 있도록 합니다.
- 메모리 관리를 자동으로 해줍니다.(GC)

</br >

## JVM 구성

![img](https://blog.kakaocdn.net/dn/c4mJl7/btqW4hK9rcN/Bn2q79ksiAxSof74i99ADk/img.png)

JVM은 크게 4가지로 구분됩니다.

1. **Class Loader**
2. **Execution Engine**
3. **JVM Memory (Runtime Data Area)**
4. **Native**

</br >

### 1. Class Loader

자바에서 소스를 작성하면 .java파일이 생성된다. 이 소스를 java컴파일러가 컴파일하면 .class파일(바이트 코드)이 생성된다.

Class Loader는 .class 파일(바이트 코드)을 로드하는데 사용되는 하위 시스템으로, 크게 세 가지 기능을 수행한다.

- Loading
  - Loading은 클래스 로드가 .class 파일을 로드합니다.
  - 그 내용에 따라 적절한 바이너리 데이터를 만들고 메서드 영역에 저장합니다.
  - 로드가 끝나면 해당 클래스 타입을 Class 객체를 생성하여 힙 영역에 저장합니다.
- Link
  - 클래스 로드가 클래스를 로드한 후 Link가 수행됩니다.
  - 바이트 코드 검증기는 .class 파일이 올바른지 여부를 확인합니다.
  - 또한 클래스에 있는 변수 및 메서드에 대한 메모리 할당을 수행합니다.
- initialiation
  - static 변수의 값을 할당합니다.
  - 이때 static 블록이 실행됩니다. 

</br >

### 2. Execution Engine

Class Loader에 의해 메모리에 적재된 .class 파일(바이트 코드)를 기계어로 변경해 명령어 단위로 실행하는 역할을 합니다.

실행하는 방식으로 총 2가지 방식이 있습니다.

- 명령어를 하나하나 실행하는 **인터프리터 방식**
- **JIT(Just-In-Time)컴파일러**를 이용하는 방식
  - JIT 컴파일러는 적절한 시간에 전체 바이트 코드를 네이티브 코드로 변경해서 Execution Engine이 네이티브로 컴파일된 코드를 실행하는 것으로 성능을 높이는 방식입니다.

</br >

### 3. JVM Memory (Runtime Data Area)

JVM Memory는 5가지 영역으로 구분할 수 있습니다.

### - Method area

- 클래스 멤버 변수의 이름, 데이터 타입, 접근 제어자 정보 같은 **필드 정보**
- 메서드의 이름, 리턴 타입, 파라미터, 접근 제어자 정보같은 **메서드 정보**
- Type 정보(Interface, class)
- Constant Pool(문자 상수, 타입, 필드, 객체 참조)
- static 변수, final class 변수 등
- 위와 같은 정보들이 저장됩니다. 여기에 저장된 정보들은 공유됩니다.

</br >

### - Heap area

- new 키워드로 생성되는 객체와 배열이 생성되는 영역입니다.
- 메서드 영역에 로드된 클래스만 생성이 가능하고 GC가 참조되지 않는 메모리를 확인하고 제거하는 영역입니다.
- 여기에저장된 정보들은 공유됩니다.

</br >

### **- Stack area**

- 스레드가 생성될 때마다 생성되는 영역으로, 지역변수가 저장됩니다.
- 메서드를 호출할 때마다 스택이 개별적으로 생성됩니다.
- 예제) Event event = new Event()
  - Stack area: Event event;
  - Heap area: new로 생성된 Event 클래스의 인스턴스
  - Stack 영역에 생성된 event 값은 힙 영역의 주소값을 가지고 있습니다. 즉, Stack 영역에 생성된 event가 Heap 영역에 생성된 객체를 가리키고(참조하고) 있습니다.

</br >

### \- PC Register

- Thread가 생성될 때마다 생성되는 영역으로, Program Counter, 즉, 현재 쓰레드가 실행되는 부분의 주소와 명령을 저장하고 있는 영역입니다.
- 이를 이용해서 쓰레드를 돌아가면서 수행할 수 있게 합니다.

### \- Native Method Stack

- 자바 언어 이외의 언어로 작성된 코드를 저장하는 메모리 영역입니다.
- 보통 C/C++ 등으로 작성된 함수들을 native 키워드를 사용하여 메서드를 호출할 수 있습니다.

</br >

**Method, Heap 영역은 모든 쓰레드가 공유합니다.**

**Stack, PC Register, Native Method Stack 은 각각의 쓰레드마다 생성되고 공유되지 않습니다.**

