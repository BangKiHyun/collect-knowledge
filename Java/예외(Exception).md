## Exception

- Error와 Exception이란
  - Error
    - 시스템에 비정상적인 상황이 생겼을 때 발생
    - System Level에서 발생하기 떄문에 심각한 수준의 오류이다
      따라서 개발자가 미리 예측하여 처리할 수 없다
  - Exception
    - 개발자가 구현한 로직에서 발생
    - 미리 예측하여 처리 할 수 있는 상황



- Checked Exception과 Unchecked(Runtime) Exception 차이
  - RuntimeException을 제외한 모든 클래스는 CheckedException이다
  - Checked Exception
    - 반드시 예외를 처리해야 한다
    - 컴파일 단계에서 확인할 수 있다
    - 예외발생시 **roll-back**을 하지 않는다!
    - IOException/ SQLException 등등
  - Unchecked Exception
    - 반드시 예외를 처리하지 않아도 된다
    - Runtime에서 확인할 수 있다
    - 예외발생시 roll-back을 해야한다
    - NullPointerException/ ArithmeticException 등등

![img](http://www.nextree.co.kr/content/images/2016/09/exception-table.png)



- 예외 처리 방법
  1. 예외 복구
     - 예외가 발생하여도 애플리케이션은 정상적인 흐름으로 진행된다는 것
     - 재시도를 통해 정상적인 흐름을 타게 한다거나, 예외가 발생하면 이를 미리 예측하여 다른 흐름으로 유도시키도록 구현하여 예외가 발생하였어도 정상적으로 작업을 종료시키게 할 수 있다
     - try{} catch{} finally{};
  2. 예외 회피
     - 예외가 발생하면 throws를 통해 호출한쪽으로 예외를 던지고 그 처리를 회피하는 것
     - 무책임하게 던지는 것은 위험하기에 신중해야하는 로직이다
     - 호출한 쪽에서 예외를 받아 처리하도록 하거나,
       해당 메서드에서 이 예외를 던지는 것이 최선의 방법이라고 확신이 있을 때만 사용
  3. 예외 전환
     - 예외를 잡아서 다른 예외를 던지는 것
     - 호출한 쪽에서 예외를 받아서 처리할 때 좀 더 명확하게 인지할 수 있도록 돕기 위한 방법이다
       어떤 예외인지 분명해야 처리가 수월해지기 때문이다



- 목적과 필요성
  - 보다 안정적이고 오류에 강한 프로그램을 만드는 것
  - 예외처리를 통해 되지 말아야할 기능이 작동한다거나 보이지 말아야 할 정보가 보이게 하지 않기위해 사용한다
  - 예외처리를 잘 해 놓으면 유지보수 측면에서 상당한 효율을 갖을 수 있다

