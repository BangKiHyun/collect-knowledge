# Primary Key vs Unique Key

## Primary Key

- 테이블의 각 튜플을 고유하게 식볋
- Primary Key는 해당 테이블의 식별자 역할을 하는 제약 조건으로 **테이블에 하나만 설정할 수 있다.**
- 다른 레코드들과 서로 다르다는 사실을 확인할 수 있도록 도와주는 역할을 한다.
  - 즉, **유일성을 보장해 준다.**
- 유일성을 보장해주기 위해서는 **Unique** 해야하면 NULL이면 안된다.(**NOT NULL**)

</br >

## Unique Key

- Unique Key는 테이블 내 항상 유일해야 하는 값을 의미한다.
  - 즉, **중복을 허용하지 않는 컬럼**이다.
- 유일해야하는 값은 레코드당 하나일 필요가 없다.
  - 즉, **한 테이블에 여러개 설정이 가능**하다.
- Unique Key는 NULL 값이 허용 된다.

</br >

## 회원 테이블 예제

- 회원 테이블이 있다고 가정할 때, 회원 이름, 주민번호, 아이디, 주소 등등 여러가지가 있다.
- **유일한 성질: 주민번호, 아이디**
  - 즉, 주민번호, 아이디 둘 중 하나를 PK로 설정할 수 있다.
- 만약 회원 이름을 Unique Key로 설정했을 때
  - 회원 이름을 가진 사람은 최초의 한 사람만 가입이 가능해진다.

</br >

# Primary Key와 Unique Key 생성 및 추가 삭제 방법

## Primary Key

### 1. 테이블 생성

~~~mysql
CREATE TABLE table_name (
    column1 int(10) PRIMARY KEY
)
~~~

~~~mysql
CREATE TABLE table_name (
    column1 int(10),
    column2 int(10),
    PRIMARY KEY (column1, column2)
)
~~~

### 2. 새로운 프라이머리 키 설정

~~~mysql
ALTER TABLE table_name ADD PRIMARY KEY (column1, column2);
~~~

### 3. 삭제

~~~sql
ALTER TABLE table_name DROP PRIMARY KEY
~~~

</br >

## Unique Key

### 1. 테이블 생성

~~~mysql
CREATE TABLE table_name (
    column1 int(10),
    column2 int(10),
    UNIQUE KEY uk_name (column1, column2)
)
~~~

### 2. 추가

~~~mysql
ALTER TABLE table_name ADD UNIQUE uk_name (column1, column2);
~~~

### 3. 삭제

~~~sql
ALTER TABLE table_name DROP INDEX uk_name
~~~

