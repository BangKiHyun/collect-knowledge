# JPA vs MyBatis

## 들어가기 전

## 영속성(Persistence)

- 데이터를 생성한 프로그램이 종료되더라도 사라지지 않는 데이터의 특성을 의미
- 영속성을 갖지 않는 데이터는 단지 메모리에서만 존재하기 때문에 프로그램을 종료하면 모두 잃어버리게 된다.

</br >

## Persistence Layer

- 프로그램의 아키텍처에서 데이터의 영속성을 부여해주는 계층
- JDBC를 이용하여 직접 구현하는 방법과 **Persistence framewok**를 이용하는 방법이 있다.

</br >

## Persistence Framwork

JDBC 프로그래밍의 복잡함이나 번거로운 없이 간단한 작업만으로 데이터베이스와 연동되는 시스템을 빠르게 개발할 수 있으며 안정적인 구동을 보장한다.

SQL Mapper와 ORM 두 종류가 있다.

### SQL Mapper

- SQL Mapper는 SQL 문장으로 직접 데이터베이스 데이터를 다룬다.
- 즉, **SQL을 직접 명시**해줘야 한다.
- ex) MyBatis, jdbcTempletes

### ORM

- 객체를 통해 간접적으로 데이터베이스 데이터를 다룬다.
- **객체와 관계형 데이터베이스의 데이터를 자동으로 매핑**해주는 것을 의미한다.
- **객체 간의 관계를 바탕으로 SQL을 직접 생성**한다.
- ex) JPA, Hibernate

</br >

## JDBC(Java Database Connectivity)

- JDBC란 DB에 접근할 수 있도록 Java에서 제공하는 API
  - 모든 Java의 Data Access 기술의 근간
  - **모든 Persistence Framework는 내부적으로 JDBC API를 사용**
- JDBC는 데이터베이스에 자료를 쿼리하거나 업데이트 하는 방법을 제공한다.

</br >

## JPA(Java Persistence API)

- 자바 ORM 기술에 대한 API 표준 명세로, Java에서 제공하는 API
  - JPA는 ORM을 사용하기 위한 표준 인터페이스를 모아둔 것이다.
- 사용자가 원하는 JPA 구현체를 선택해서 사용할 수 있다.
  - 대표적은 구현체로 Hibernate가 있다.

### Hibernate

- JPA의 구현체 중 하나
- Hibernate가 SQL을 직접 사용하지 않는다고 해서 JDBC API를 사용하지 않는 것은 아님.
  - Hibernate가 지원하는 메서드 내부에서 JDBC API가 동작하고 있으며, 단지 개발자가 직접 SQL을 작성하지 않을 뿐이다.

### 장점

- 객체지향적으로 데이터를 관리할 수 있기 때문에 **비즈니스 로직에 집중 할 수 있다.**
- 테이블 생성, 변경, 관리가 쉽다.
- 로직을 쿼리에 집중하기 보다 객체자체에 집중 할 수 있다.

### 단점

- 학습하는데 시간이 걸릴 수 있다.
- 잘 이해하고 사용하지 않으면 데이터 손실이 있을 수 있다.
- 성능상 문제가 있을 수 있다.

</br >

## MyBaits

- 개발자가 지정한 SQL, 저장 프로시저와 몇 가지 고급 매핑을 지원하는 SQL Mapper
- JDBC로 처리하는 상당 부분의 코드와 파라미터 설정 및 결과 매핑을 대신해줌
  - 기존 JDBC를 사용할 때 DB와 관련된 여러 복잡한 설정(Connection)들을 다루어야 했다.
  - 하지만 SQL Mapper는 자바 객체를 실제 SQL문에 연결함으로써, 빠른 개발과 편리한 테스트 환경을 제공한다.
- 데이터베이스 record에 원시 타입과 Map 인터페이스 그리고 자바 POJO를 설정해 매핑하기 위한 xml과 Annotation을 사용할 수 있다.

### iBatis와 차이점

- iBatis에서는 JDK 1.4 이상, MyBatis에서는 JDK 1.5 이상 사용 가능하다.
- 패키지 내부 구조 변경 com.ibatis.* :arrow_right: org.apache.ibatis.*
- parameterMap 사용 불가, :arrow_right: parameterType으로 대체
- MyBatis에서 Annotation을 사용할 수 있다.
  - sqlMapClient Di 설정 불필요

### 장점

- 쿼리문을 xml로 분리 가능하다.
- 복잡한 쿼리문 작성이 가능하다.
- 데이터 캐싱기능으로 성능을 향상 시킬 수 있다.

### 단점

- 비슷한 쿼리를 남발한다.
  - CRUD 메서드 직접 다 작성
- 객체와 쿼리문 모두 관리해줘야 한다.