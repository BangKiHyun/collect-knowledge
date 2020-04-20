## 트랜잭션 격리 수준(Isolation Level)

### 트랜잭션 격리 수준이란?

- 동시에 여러 트랜잭션이 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있도록 허용할지 말지를 결정하는 것이다.
- 즉, 특정 트랜잭션이 다른 트랜젹션에 update, select, insert, delete 를 허용할지 말지 결정하는 것이다.



격리 수준은 크게 4가지로 나뉜다.

- READ UNCOMMITTED
- READ COMMITTED
- REPEATABLE READ
- SERIALIZABLE

4개의 격리 수준에서 순서대로 뒤로 갈수록 각 트랜잭션 간의 데이터 격리(고립) 정도가 높아지고, 동시성은 떨어진다(성능저하).

일번적은 온라이 서비스에서는 READ COMMITTED와 REPEATABLE READ 둘 중 하나를 사용한다.



### 각각의 격리 수준에서 발생할 수 있는 이상 현상 3가지

| 유형                | 내용                                                         |
| ------------------- | ------------------------------------------------------------ |
| DIrtry Read         | 트랜잭션 T1에서 A=5로 update 하고 아직 commit를 않았는데 다른 트랜잭션 T2가 이 A값을 읽을 수 있도록 허용하는 경우 발생<br/>즉, T1이 update를 수행한 후 commit을 하지 않았는데 T2가 A를 select했을 때 commit하지 않은 값이 조회되는 경우를 뜻한다. |
| Non Repeatable Read | T1 트랜잭션이 같은 쿼리를 두번 실행했는데 그 결과값이 다른 경우, <br/>즉 T1 이  select 를 두번 하는 사이에 T2 트랜잭션이 **update 나 delete** 를 한 경우 |
| Phantom Read        | T1 트랜잭션이 같은 쿼리를 두번 수행 시 첫번째 실행시에 없던 레코드가 두번째 실행시에 튀어 나오는 경우 |



### 4가지 격리 수준

| 유형             | 내용                                                         | 읽기 이상 현상                                               |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| READ UNCOMMITTED | 트랜잭션 T1이 아직 commit하지 않은 데이터를 다른 트랜잭션 T2가 조회하는 것을 하용 | Dirty Read 발생                                              |
| READ COMMITTED   | 트랜잭션 T1이 commit한 데이터만 다른 트랜잭션 T2가 조회하는 것을 하용 <br />오라클과 같은 DBMS에서 주로 사용 | Dirty Read 방지<br />Non Repeatable Read와 Phantom Read 발생 |
| REPEATABLE READ  | 선행 트랜잭션 T1이 읽은 데이터는 T1이 종료될 때 까지 다른 트랙잭션이 수정/삭제를 허용하지 않음, 단 삽입은 허용<br />MySQL에서 주로 사용 | Dirty Read 및 Non Repeable Read 방지<br />Pahntom Read 발생  |
| SERIALIZABLE     | 선행  트랜잭션 T1이 읽은 데이터는 T1이 종료될 때 까지 다른 트랜잭션이 수정/삭제/삽입 불가 | 모든 이상현상 방지<br />완벽하지만 실제 현실적으로는 불가능  |

