## garbage collection

- Java Virtual Machine(JVM)

  - C나 C++에서는 OS레벨에서 메모리에 직접 접근하기 때문에 메모리를 명시적으로 해제해주어야 한다. 그렇지 않으면 momory leak(누수현상)이 발생하게 되고, 현재 실행중인 프로그램에서 momory leak이 발생하면 다른 프로그램에도 영향을 끼칠 수 있다
  - 반면, Java는 OS의 메모리 영역에 직접적으로 접근하지 않고 JVM이라는 가상머신을 이용해서 **간접적으로 접근**한다. JVM은 C로 쓰여진 또 다른 프로그램인데, 오브젝트가 필요해지지 않는 시점에서 알아서 메모리를 확보한다.
    이렇게 Java는 가상머신을 사용함으로써 (운영체제로 부터 독립적) OS레벨에서의 memory leak은 불가능하게 된다는 장점이 있다.
    Java가 메모리 누수현상을 방지하는 또 다른 방법이 Garbage Collection이다

  

- Garbage Collection

  - 가비지 컬렉션이란 프로그래머가 힙을 사용할 수 있는 만큼 자유롭게 사용하고, **더 이상 사용되지 않는 오브젝트**들은 가비지 컬렉션을 담당하는 프로세스가 **자동으로 메모리에서 제거**하도록 하는 것이다

  - 자바는 가비지 컬렉션에 아주 단순한 규칙을 적용한다
    Heap 영역의 오브젝트 중 **Stack에 도달 불가능한(Unreachable) 오브젝트들**은 가비지 컬렉션의 대상이 된다

  - JVM의 Garbage Collection는 Unreachable Object를 우선적으로 메모리에서 제거하여 메모리 공간을 확보한다. Unreachable Object란 Stack 도달할 수 없는 Heap 영역의 객체를 말한다.

  - Garbage Collection 과정은 Mark and Sweep 라고도 한다.

    - Mark : JVM의 Garbage Collector 가 스택의 모든 변수를 스캔하면서 각각 어떤 오브젝트를 Reference(참조)하고 있는지 찾는 과정
    - marking : Reachable Object가 레퍼런스하고 있는 오브젝트
      첫 번째 단계인 marking작업을 위해 모든 스레드는 중단되는데 이를 stop the world 라고 부르기도한다
    - Sweep : mark 되어있지 않은 모든 오브젝트들을 힙에서 제거하는 과정

    한마디로, Garbage Collection이란 garbage가 아닌 것을 따로 mark하고 그 외의 것은 모두 지우는 것이다. 만약 힙에 garbage만 가득하다면 제거 과정은 즉각 이루어진다