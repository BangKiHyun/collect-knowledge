# SQL Injection

## SQL Injection이란

이용자의 입력값이 SQL 구문의 일부로 사용될 경우, 해커에 의해 조작된 SQL 구문이 데이터베이스에 그대로 전달되어 비정상적인 DB 명령을 실행시키는 공격 기법입니다.

웹 애플리케이션이 데이터베이스와 연동되는 모델에서 발생 가능합니다.

</br >

## SQL Injection이 발생하는 이유

SQL Injection은 데이터베이스 명령어인 SQL 쿼리문에 기반하여 공격을 수행합니다. 공격에 이용되는 쿼리문은 문법적으로 정상적인 SQL 구문이지만, 실행되지 말아야 할 쿼리문이 실행되어 공격에 이용됩니다.

SQL Injection은 다음과 같은 조건을 충족하면 공격이 가능합니다.

1. 웹 애플리케이션이 DB와 연동하고 있다.
2. 외부 입력값이 DB 쿼리문으로 사용된다.

위 두 조건중 하나라도 충족하지 않는다면 SQL Injection공격은 소용이 없습니다. 하지만 대부분의 웹 애플리케이션은 두 조건을 충족합니다. 그래서 대부분의 경우 SQL Injection은 유효한 공격이 될 수 있습니다.

</br >

## SQL Injection 공격 유형

## 1. 논리적 에러를 이용한 SQL Injection

논리적 에러를 이용한 SQL Injectino은 가장 많이 쓰이고, 대중적인 공격 유형입니다.

## 1-1 Where 구문 우회

Where 구문은 SQL에서 조건을 기술하는 구문입니다. Where 조건을 **참(true)**이 되는 범위만 쿼리 결과로 반환합니다.

이 Where 조건을 무조건 참이 되도록 조작하여 Where 조건을 우회하게 만듭니다.

예를 들어, 로그인 처리를 위한 쿼리가 있다고 가정해보겠습니다. 외부 입력값인 id와 password가 쿼리문의 일부로 사용되고 있습니다.

~~~sql
SELECT * FROM Users WHERE id = 'id' AND password = 'password'
~~~

### 주석 삽입

SQL 구문에 주석을 의도적으로 삽입하여 Where 조건을 무력화할 수 있습니다. 

대신 한가지 조건이 있습니다. 가입되어있는 id를 이미 알고 있어야 합니다.

~~~mysql
[입력값]
id: userId'--
password: 아무거나

[실행 쿼리문]
SELECT * FROM Users WHERE id = 'userId'-- AND password = '아무거나'
~~~

위 예제는 id값이 주석을 삽입했습니다. 주석 이하의 구문은 실행되지 않기 떄문에 userId라는 계정의 패스워드를 몰라도 인증을 통과하게 됩니다.

</br >

### or 조건을 이용한 true식

쿼리문이 항상 참이 되도록 Boolean식을 구성하여 Where 조건을 무력화 할 수 있습니다.

~~~sql
[입력값]
id: userId
password: 1234' or '1'='1

[실행 쿼리문]
SELECT * FROM Users WHERE id = 'userId' AND password = '1234' or '1'='1'
~~~

위와 같이 참인 조건이 or로 연결되도록 삽입하여 쿼리의 결과가 무조건 참이되어 인증을 통과하게 됩니다.

</br >

## 1-2 Union 명령어를 활용한 고의적 에러 유발 후 정보 획득

기본적으로 웹 애플리케이션은 쿼리 수행 중 오류가 발생하면 DB오류를 그대로 브라우저에 출력합니다. 이 오류 정보를 통해 DB의 스키마 정보나 데이터가 유출될 수 있습니다.

이 오류 정보에 기반하여 유용한 정보를 알아차릴 수 있습니다.

SQL 쿼리문에서 UNION은 두 테이블의 결과를 합치는 명령어입니다. UNION으로 합쳐지는 두 테이블은 컬럼 갯수가 일치해야만 오류가 나지 않습니다. 

예를 들어, Users 테이블은 4개의 컬럼으로 구성되어있다고 가정해보겠습니다. 그리고 다음과 같이 1개의 컬럼을 가진 테이블을 가졍하여 UNION 구문을 삽입해 보겠습니다.

~~~mysql
[입력값]
id: userId' UNION SELECT 1 --
password: 아무거나

[실행 쿼리문]
SELECT * FROM Users WHERE id = 'userId' UNION SELECT 1 -- AND password = '아무거나'
~~~

Users 테이블과 컬럼 갯수가 일치하지 않아 다음과 같은 오류가 발생합니다.

`"UNION, INTERSECT 또는 EXCEPT 연산자를 사용하여 결합된 모든 쿼리의 대상 목록에는 동일한 개수의 식이 있어야 합니다."`

컬림 갯수를 찾기위해 컬럼 갯수를 늘려가다보면 UNION SELECT 구문이 오류가 발생하지 않음을 찾고 컬럼 갯수를 찾을 수 있습니다.

이렇게 알아낸 컬럼 수를 기반으로 다음과 같이 SQL 구문을 주입하면 현재 데이터베이스에 존재하는 모든 테이블 목록을 볼 수 있습니다.

~~~sql
[입력값]
id: userId' UNION SELECT 1,1,1,1 --
password: 아무거나

[실행 쿼리문]
SELECT * FROM Users WHERE id = 'userId' UNION SELECT 1,1,1,1 -- AND password = '아무거나'
~~~

위와 같이 컬럼 갯수를 알아 낸 후 필요한 컬럼을 찾기 위해서는 데이터 형을 알아야 합니다. 정리하자면 Union Injection을 성공시키기 위해서는 다음 조건을 충족해야 합니다.

1. Union하는 두 테이블의 컬럼 수가 같아야 한다.
2. Union하는 두 테이블의 데이터 형이 같아야 한다.

</br >

## 2. Blind SQL Injection

Blind SQL Injection은 웹 페이지가 어떠한 오류도 출력하지 않고 쿼리 결과 리스트도 제공하지 않을때 사용할 수 있는 공격 기법입니다. 한마디로, Blind SQL Injection은 쿼리 결과의 참 or 거짓으로부터 DB의 값을 유출해 내는 기법입니다.

이 공격을 수행하려면, 먼저 웹 애플리케이션에서 쿼리 결과에 대해 참 or 거짓을 반환하는 요소를 찾아야 합니다.

## 2-1 Boolean based Blind 공격

어느 웹사이트가 게시판 검색이라는 기능을 제공한다고 가정해보겠습니다. 그럴 경우 다음과 같이 참 or 거짓 반환을 테스트해 볼 수 있습니다.

~~~mysql
[입력값]
제목검색: hello' AND 1=1 -- //항상 참인 조건 삽입

[결과]
게시판 검색됨 --> 참(true)으로 간주

[입력값]
제목검색: hello' AND 1=2 -- //항상 거짓인 조건 삽입

[결과]
게시물 검색 안됨 --> 거짓(false)로 간주
~~~

'hello'라는 검색에 항상 참인 조건(1=1)을 AND로 조합했을 때 값이 정상적으로 반환되고, 동일한 검색어(hello)에 항상 거짓인 조건(1=2)을 AND로 조합했을 때 값을 반환하지 않았습니다. 이 둘을 참과 거짓의 반응으로 간주할 수 있습니다.

여기에 AND 조건으로 해커가 알고 싶은 쿼리 조건을 삽입해서 그 결과로부터 정보유출을 가능하게 할 수 있습니다.

AND 조건에 논리식을 대입하여 참 or 거짓 여부를 알아내는 방식을 Boolean Basend Bline 공격이라 합니다.

</br >

## 2-2 Time based Blind 공격

Time based Blined은 시간을 지연시키는 쿼리를 주입하여 응답 시간의 차이로 참 or 거짓 여부를 판별할 수 있습니다.

만약 참이라는 응답 시간이 딜레이되고, 거짓이라면  응답이 즉시 이뤄집니다.

~~~mysql
[입력값]
제목검색: hello AND sleep(5)#'

[실행 쿼리]
SELECT * FROM Boards WHERE TITLE = 'hello' AND sleep(5)

[결과]
1) 응답이 5초 지연 -> 참(true) --> hello 검색어가 존재함
2) 응답이 즉이 이뤄짐 -> 거짓(true) --> hello 검색어가 존재하지 않음
~~~

위와같이 응답 시간에 걸리는 시간으로 참 or 거짓 여부를 판별합니다.

