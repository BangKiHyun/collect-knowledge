# [DB] Connection Pool

서버는 동시에 사용할 수 있는 사람의 수라는 개념이 존재합니다. 만약에 동시 접속자 수를 초과하게 될 경우 어떻게 될까요?

동시 접속자 수를 초과하게 된다면 에러(예외)가 발생할 겁니다. 예외가 발생하면 그 접속자는 더이상 처리를 하지 못하므로, 사이트 이용자는 다시 접속을 해야하는 불편함이 있습니다.

이를 해결하기 위해 탄생한 것이 Connection Pool 입니다. 

</br >

## Connection Pool이란?

Connection Pool이란 동시 접속자가 가질 수 있는 Connection을 하나로 모아놓고 관리한다는 개념입니다.

누군가 접속하면 자신이 관리하는 Pool에 남아있는 Connection을 제공합니다. 하지만 남아있는 Connection이 없는 경우라면 해당 클라이언트는 대기 상태로 전환시킵니다. 대기하고 있다가 누군가가 다 사용하고 Connection을 반납하여 다시 Pool에 들어오면 대기 상태에 있던 클라이언트에게 순서대로 제공합니다.

즉, 데이터베이스와 연결된 Connection을 미리 만들어서 Pool속에 저장해 두고 있다가 필요할 때 Connection을 Pool에서 쓰고 다시 Pool에게 반환하게 됩니다.

![img](https://blog.kakaocdn.net/dn/SJqrq/btq7YolUTL0/98SJJYYvTqWc9WzHjNxfM0/img.png)

위 그림과 같이 Pool에 미리 생성되어 있는 Connection을 가져다가 사용하고, 사용이 끝나면 Connection을 Pool에 반환합니다.

웹 프로그램에서는 데이터베이스의 환경설정과 연결 관리 등을 따로 XML파일이나 속성 파일을 사용해서 관리하고, 이렇게 설정된 정보를 이름을 사용하여 획득하는 방법을 사용합니다.

웹 컨테이너(WAS)가 실행되면서 Connection객체를 Pool에 생성해 둡니다. 미리 생성해두기 때문에 데이터베이스에 부하를 줄이고 유동적으로 연결을 관리할 수 있습니다.

</br >

## 장점

- 동시 접속자 수가 많은 서버에서 오버헤드를 방지할 수 있습니다.
  - 한 번에 생성될 수 있는 Connection을 제어하기 때문에 동시 접속자 수가 몰려도 웹 어플리케이션이 쉽게 다운되지 않습니다.
- Pool속에 미리 Connection이 생성되어 있기 때문에 Connection을 생성하는데 드는 연결 시간이 소비되지 않습니다.
  - Connection을 생성하고 닫는 시간이 소모되지 않기 때문에 그만큼 어플리케이션 실행 속도가 빨라집니다.

</br >

## 주의할 점

Connection Pool을 너무 크게 해놓으면 메모리 소모가 크고, 적게 해놓으면 Connection이 많이 발생할 경우 대기시간이 늘어나기 때문에 웹 사이트 동시 접속자 수 등 서버 부하에 따라 크기를 알맞게 조정해야 합니다.

</br >

## 참고

- https://brownbears.tistory.com/289
- https://linked2ev.github.io/spring/2019/08/14/Spring-3-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80%EC%9D%B4%EB%9E%80/

