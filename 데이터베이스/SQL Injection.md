# SQL Injection

## SQL Injection이란

- SQL Injection 이란 악의적인 사용자가 보안상의 취약점을 이용하여, 임의의 SQL 문을 주입하고 실행되게 하여 데이터베이스가 비정상적인 동작을 하도록 조작하는 행위
- 공격이 비교적 쉬운 편이고 공격에 성공할 경우 큰 피해를 입을 수 있는 공격이다.

</br >

## SQL Injection이 발생하는 이유

- 웹 어플리케이션은 User의 행동(클릭, 입력 등)에 따라 DB에 있는 데이터를 서로 다르게 표시한다.
- 이를 위히 Query는 User가 입력한 데이터를 포함하여 Dynamin하게 변하므로 개발자가 의도하지 않은 정보를 열람할 수 있게 된다.

## 공격 종류

### 1. 논리적 에러

논리적 에러를 이용한 SQL Injectino은 가장 많이 쓰이고, 대중적인 공격이다.

</br >

### 예제

- 일반적인 쿼리: SELECT * FROM Users WHERE id = 'id' AND password = 'password'
- SQL Injection 쿼리: SELECT * FROM Users WHERE id = '**' OR 1=1 --** ' AND password = 'password'

주입된 내용은 `'OR 1=1 --` 로 WHERE 절에 있는 싱글쿼터를 닫아주기위 싱글쿼터와 `OR1=1` 라는 구문을 이용해 WHERE 절을 모두 참으로 만들고, `--` 를 넣어줌으 뒤의 구문을 주석처리 해주었다.

결론적으로 Users 테이블에 있는 모든 정보를 조회하게 됨으로써 **가장 먼저 만들어진 계정**으로 로그인에 성공하게 된다.

</br >

### 2. Union 명령어

- SQL에서 Union 키워드는 두 개의 쿼리문에 대한 결과를 통합해서 하나의 테이블로 보여주게 하는 키워드다.
- 정상적인 쿼리문에 Union 키워드를 사용하여 인젝션에 성공하면, 원하는 쿼리문을 실행할 수 있게 된다.

Union Injection을 성공하기 위해서는 두 가지 조건이 있다. 

1. Union하는 두 테이블의 컬럼 수가 같아야 한다.
2. Union하는 두 테이블의 데이터 형이 같아야 한다.

</br >

### 예제

- 일반적인 쿼리: SELECT * FROM Board WHERE title LIKE '%INIPUT%' OR contents '%INPUT%'
- SQL Injection 쿼리: SELECT * FROM Board WHERE title LIKE '% **'UNION SELECT null, id, passed FROM Users --** %' OR contents '%INPUT%'

주입된 내용은 `'UNION SELECT null, id, passed FROM Users --`로 Union 키워드와 함꼐 컬럼 수를 맞춰서 SELECT 구문을 넣어주게 되면 두 쿼리문이 합쳐져서 하나의 테이블로 보여지게 된다.

위 인젝션이 성공하게 되면, 사용자의 개인정보가 게시글과 함께 화면에 보여지게 된다.

</br >

### 3. Blind based SQL

- Blind SQL Injection은 데이터베이스로부터 특정한 값이나 데이터를 전달받지 않고, 단순히 참과 겆시의 정보만 알 수 있을 때 사용한다.
- 로그인 폼에 SQL Injection이 가능하다고 가정 했을 때, 서버가 응답하는 로그인 성공과 로그인 실패 메시지를 이용하여, DB의 테이블 정보 등을 추출해 낼 수 있다.