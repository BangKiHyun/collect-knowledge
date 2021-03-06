## 모니터(Monitor)

- 동기화 문제를 해결하기 위한 도구로 세마포어의 업그레이드 버전
- 특히 자바에서 모니터에 대한 활용이 높음
- 모니터의 경우 2개의 queue를 사용
  - 배타 동기 queue(모니터 진입부)
    - 하나의 쓰레드만 공유자원에 접근할 수 있게 하는 작용을 하는 공간
    - 특정 쓰레드가 공유자원을 사용하는 함수를 사용하고 있으면 다른 쓰레드는 접근할 수 없고 대기
  - 조건 동기 queue
    - 진입 쓰레드가 블록되면서 새 쓰레드가 진입가능하게 하는 공간
    - 새 쓰레드는 조건동기로 볼록된 쓰레드를 깨울 수 있고, 깨워진 쓰레드는 현재 쓰레드가 나가면 재진입 할 수 있음
- ex) Java
  - 공통 자원을 사용할 경우 배타 동기를 선언하는 synchronized라는 키워드를 적어주면 됨
  - 조건 동기의 경우 wait()함수를 실행하면 진입 쓰레드를 조건 동기 queue에 블록 시킴
  - notify()함수는 블록된 함수를 깨우는데 새로운 쓰레드가 실행하는 방식으로 깨움
  - notifyAll()은 모든 쓰레드를 깨우는 것으로 사용