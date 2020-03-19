## Synchronization

- Race Condition : 여러 주체(객체)가 하나의 데이터(공유 자원)을 동시에 접근하려고 할 때
- 공유데이터(shared data)의 동시접근(consistency access)는 데이터의 불일치 문제를 발생시킴
  이것을 조율해줘야 하는 문제가 발생
  공유 메모리를 사용하는 프로세스 사이에서도 발생 가능성 있음
- 즉 어떤 공유데이터를 접근하는 경우 그들의 순서를 조절하는 것을 Synchronization이라고 한다



- 해결방법

  - 상호배제(Mutual exclusion)

    - 공유데이터가 하나의 프로세스에 의해 독점적으로 사용되는 원칙
      critical section은 상호배제의 원리가 지켜져야 하는 영역을 의미한다

    - Critical Section(임계 구역)

      - 공유데이터에 동시접근하려고 하는 문제가 발생하지 않게 독점을 보장해줘야 하는 영역
      - 임계 구역은 지정된 시간이 지난 후 종료되게 해야한다
      - 스레드가 공유자원의 배타적인 사용을 보장받기 위해 임계 구역에 들어가거나 나올 때에는 세마포어 같은 동기화 메커니즘니 꼭 필요(세마포어는 락에서 발전된 동시접근 해결방법)

      entry section
              critical section
      exit section

    - 프로세스는 critical section안으로 들어가기 이전 entry section단계에서 출입을 허가 받아야 한다
      그이유는 입계 구역 원칙을 지켜야 하기 때문에!

    - 상호배제의 장점은 혼란이 없고 간단하는 점.  하지만 매우 커다란 문제점이 있다
      read와 write처리에 관한 문제로, 공유 데이터에 read라는 명령어를 수행하기 위해 점근하는 프로세스는 100개가 동시에 critical section으로 들어와도 아무 문제가 없다(read는 공유된 데이터를 참조할 뿐 아무 변화도 가져오지 않기 때문) 그러나 write명령어를 수행하기 위해 접근하는 프로세스의 경우 1개만이 critical에서 처리되어야 한다(write는 공유 데이터를 변화기시키 때문)

    - 상호베제는 read와 write의 처리를 구분 짓지 않는다. 무조건 공유 데이터에 접근하기 위해 100개가 동시 처리되어도 좋은 read마저도 오랜 기다림을 가질 수 있다

    - 해결방법으로 kernel자체가 하나의 커다란 critical section의 역할을 하면 된다

    ![1576568266625](C:\Users\rlrlv\AppData\Roaming\Typora\typora-user-images\1576568266625.png)

  - Lock이란

    - 하나의 프로세스가 공유데이터에 접근하는 동안 다른 프로세스가 공유데이터에 접근하지 못하도록 입구를 잠궈 두는 것
    - spin lock
      - spin lock이 0일때 critical section에 프로세스가 들어와도 된다는 것 의미
        spin lock이 1일때 critical section에 다른 프로세스가 들어와 있다는 것을 의미
      - exit section에의해 critical section내에서 프로세스가 처리되었다는 것을 인식 -> spin lock 0으로 만듬
        ![1576568568876](C:\Users\rlrlv\AppData\Roaming\Typora\typora-user-images\1576568568876.png)
    - sleep lock
      - lock이 1일 경우 기다리던 프로세스는 sleep을 하게 되는 것
        critical section내에 프로세스가 언제 처리가 끝날지 모르기 때문
      - critical section내에 프로세스 작업이 끝나게 되면 sleep중인 프로세스를 깨워 처리하게 된다
        ![1576568692209](C:\Users\rlrlv\AppData\Roaming\Typora\typora-user-images\1576568692209.png)
      - sleep lock 문제점
        - 동시에 2개 이상의 프로세스가 접근해 0을 1로 바꿀 가능성이 있다/ 하나 이상의 프로세스가 동시에 lock=1에서 lock=0으로 바뀐 변수를 확인한 경우 발생
        - A프로세스와 B프로세스가 동시에 lock=0인 것을 read한다. 그러나 프로세스A가 먼저 lock=1로 write하고 프로세스B는 read만을 한 채 더 이상 작업이 진행되지 않았다. 이럴 경우 B는 context switching이 일어나게 되고 다음 작업이 수행될 때 read작업을 다시 하지 않는다. 그렇기에 context switching이 발생하기 이전 lock=0이었던 것만을 기억한 채 작업을 수행하기 때문에 현재 lock=1이라도 critical section에 접근하게 되는 것이다
        - 해결방법으로 read/write를 하나의 명령어로 만들어 놓는 것이다. 이렇게 되면 동시에 2개의 프로세스가 lock을 0으로 볼 수 없다

- 정리

  - 필요한 이유 : 여러 프로세스가 공유자원에 동시접근할 경우 예측하지 못한 겨로가가 나올 가능성이 있기때문에 이러한 가능성을 배제하기위해 필요
  - 목적 : 데이터의 일관성을 보장해 주는것!
  - 일관성 보장 방법 : 1) 상호배제 : 공유데이터가 하나의 프로세스에 의해 독점적으로 사용되는 원칙
        2) kernel 이용 : spin lock과 sleep lock을 이용한 kernel 자체가 critical section역할