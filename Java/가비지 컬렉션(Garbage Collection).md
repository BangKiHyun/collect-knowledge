# 가비지 컬렉션(Garbage Collection)

## 들어가기

- C나 C++에서는 OS레벨에서 메모리에 직접 접근하기 때문에 메모리를 명시적으로 해제해주어야 한다.
  - 그렇지 않으면 momory leak(누수현상)이 발생하게 되고, 현재 실행중인 프로그램에서 momory leak이 발생하면 다른 프로그램에도 영향을 끼칠 수 있다
- Java는 OS의 메모리 영역에 직접적으로 접근하지 않고 JVM이라는 가상머신을 이용해서 **간접적으로 접근**한다.
  - JVM은 C로 쓰여진 또 다른 프로그램인데, 오브젝트가 필요해지지 않는 시점에서 알아서 메모리를 확보한다.
  - Java는 가상머신을 사용함으로써 (운영체제로 부터 독립적) OS레벨에서의 memory leak은 불가능하게 된다는 장점이 있다.
  - Java가 메모리 누수현상을 방지하는 또 다른 방법이 **Garbage Collector**이다

</br >

## GC란?

- **주소를 잃어버려서 사용할 수 없는 메모리**를 말한다. 즉, **정리되지 않은 메모리**를 뜻한다.
- GC는 프로그래머가 힙을 사용할 수 있는 만큼 자유롭게 사용하고, **더 이상 사용되지 않는 오브젝트**들은 가비지 컬렉션을 담당하는 프로세스가 **자동으로 메모리에서 제거**하도록 하는 것이다.
- C++와 같은 다른 언어에서는 사용하지 않을 객체의 메모리는 직접 해제해줘야 하지만 자바는 GC가 잡아주기 때문에 개발자 입장에서 편리하다.

</br >

## 언제 실행 되나?

- JVM은 OS에게 메모리를 부여받고 프로그램들을 실행시킨다. 그러다 메모리가 부족해지면 OS에게 추가로 메모리를 더 요청하게 된다.
- 여기서 **메모리를 더 덜라고 요청할 때 가비지 GC가 실행**된다.

</br >

## Stop The World

- Stop the world는 GC 실행을 위해 JVM이 애플리케이션 실행을 멈추는 것이다.
- GC가 실행될 때, GC를 실행하는 쓰레드를 제외한 모든 스레드들이 작업을 멈춘다.
- GC 작업이 완료된 후 중단헀던 작업을 다시 시작한다.
  - 그래서 보통 GC 튜닝은 stop the world 시간을 줄이는 것을 말한다.

</br >

## GC 과정 (Mark and Sweep)

- GC 과정을 Mark and Sweep 이라고도 한다.
- Mark
  - JVM의 Garbage Collector 가 스택의 모든 변수를 스캔하면서 각각 어떤 오브젝트를 Reference(참조)하고 있는지 찾는 과정
  - Mark 작업을 위해 모든 스레드는 중단된다. 즉, Stop the world가 발생한다.
- Sweep : mark 되어있지 않은 모든 오브젝트들을 힙에서 제거하는 과정

</br >

## GC의 대상이 되려면?

- GC는 가비지 객체를 판별하기 위해 reachablility라는 개념을 사용한다.
  - 어떤 객체에 유효헌 참조가 있으면 `reachable`
  - 참조가 없으면 `unreachable`로 구별
    - `unreachable`객체를 GC의 대상으로 간주
- **여기서 말하는 `reachable`은 `Stack`에서 `Heap`영역의 객체에 대해 참조 할 수 있는가를 얘기한다.**
- 만약 힙에 garbage만 가득하다면 Mark하는 과정 없이 제거 과정이 즉각 이루어진다.

