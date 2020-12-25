# ORM(Object Relation Mapping)

## ORM이란?

- ORM은 **사물을 추상화시켜 이해하려는 OOP적 사고방식과 DataModel을 정형화하여 관리하려는 RDB 사이를 연결할 계층의 역할**로 제시된 패러다임
- RDB의 모델을 OOP에 Entity 형태로 투영시키는 방식을 사용한다.
- 데이터베이스 데이터 <--매핑--> Object 필드
  - 객체를 통해 간접적으로 데이터베이스 데이터를 다룸

</br >

## ORM의 장점

### 객체 지향적인 코드로 인해 더 직관적이고 비즈니스 로직에 더 집중할 수 있게 도와준다.

- ORM을 이용하면 SQL Query가 아닌 직관적인 코드로 데이터를 조작할 수 있어 개발자가 개체 모델로 프로그래밍하는 데 집중할 수 있도록 도와준다.
- 선언문, 할당, 종료 같은 부수적인 코드가 없거나 급격히 줄어든다.
- 각종 객체에 대한 코드를 별도로 작성하기 때문에 코드의 가독성을 줄여준다.
- SQL의 절차적이고 순차적인 접근이 아닌 객체 지향적인 접근으로 인해 생산성이 증가한다.

### 재사용 및 유지보수의 편리성이 증가한다.

- ORM은 독립적으로 작성되어있고, 해당 객체들을 재활용 할 수 있다.
- 매핑정보가 명확하며, ERD를 보는 것에 대한 의존도를 낮출 수 있다.

### DBMS에 대한 종속성이 줄어든다.

- 객체 간의 관계를 바탕으로 SQL을 자동으로 생성하기 떄문에 DMBS의 데이터 구조와 Java의 객체지향 모델 사이의 간격을 좁힐 수 있다.

- 대부분 ORM 솔루션은 DB에 종속적이지 않다.

  - 종속적이지 않다는것은 구현 방법 뿐만아니라 많은 솔류션에서 자료형 타입까지 유효하다.

- 프로그래머는 Object에 집중함으로 극단적으로 DBMS를 교체하는 거대한 작업에도 비교적 적은 리스크와 시간이 

  소요된다.(설정만 바꿔주면 되기 떄문)

</br >

## ORM의 단점

### 세밀함의 불일치

- 경우에 따라 **데이터베이스에 있는 해당 테이블 수보다 더 많은 클래스를 가진 객체 모델을 가질 수 있다.**
- ex) "사용자 세부 사항"
  - 사용자 세부 사항에 대해 Person과 Address **두 개의 클래스**를 나눴다.
  - 하지만 데이터베이스에는 Person이라는 **하나의 테이블**에 "사용자 세부 사항"을 저장할 수 있다.
  - 이렇게 2개의 Object와 1개의 Table로 세밀함이 불일치 할 수 있다.

### 하위 타입 문제

- RDB에는 상속의 개념이 없다.

### 동일성 문제

- Java의 경우 Equals로 평가하지만, DB는 PK값으로 평가한다.

### 연관 관계 문제

- 객체지향 언어는 객체 참조를 사용하여 연관성을 나타낸다.
- RDB는 FK를 통해 연관성을 나타낸다.

### 데이터 검색 문제

- Java에서는 그래프 형태로 데이터를 탐색한다.
  - ex) user.getName().getLength()
- RDB는 쿼리 수 최소화를 위해 JOIN을 통해 여러 엔티티를 로드하고 원하는 대상 엔티티를 선택(select)한다.