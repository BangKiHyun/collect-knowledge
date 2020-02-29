## Java Collection 인터페이스

- Java 컬렉션 프레임웤의 상속구조

![1582963142026](C:\Users\rlrlv\AppData\Roaming\Typora\typora-user-images\1582963142026.png)



1. Set
   순서를 갖고있지 않다
   중복을 제거한다
   - HashSet
     - 가장 빠른 임의 접근 속도
     - 순서를 전혀 예측할 수 없음
   - LinkedHashSet
     - 추가된 순서, 또는 가장 최근에 접귾나 순서대로 접근 가능
   - TreeSet
     - 정렬된 순서대로 보관하며 정렬 방법을 지정할 수 있음
2. List
   인덱스를 갖고있는 값을 갖고있다
   순서를 갖고있어야 할 때 사용한다
   - ArrayList
     - 배열과 비슷한 형태
       그러나 배열의 크기를 미리 지정하지 않기 때문에 얼마든지 많은 수의 값을 저장 가능
     - 상당히 빠르고 크기를 마음대로 조절할 수 있는 배열
     - 단방향 포인터 구조로 자료에 대한 순차적인 접근에 강점이 있음
   - Vertor
     - ArrayList에 구형버전으로, 모든 메소드가 동기화 되어있음(잘 쓰이지 않음)
   - LinkedList
     - 양방향 포인터 구조로 데이터의 삽입, 삭제가 빈번할 경우 빠른 성능을 보장
     - 스택, 큐, 양방향 큐 등을 만들기 위한 용도로 쓰임
3. Queue
   - PriorityQueue



1. Map 
   Map은 Java Collection이 아니라 따로 나눠져있다
   key-value 형태로 저장하는 객체

   - HashMap
     - Map 인터페이스를 구현하기 위해 해시테이블을 사용한 클래스
     - 중복을 허용하지 않고 순서를 보장하지 않음
     - key와 value로 null허용
   - HashTable
     - HashMap보다 느리지만 동기화 지원
     - key와 value로 null 허용 안됨
   - TreeMap
     - 이진트리 형태로 key-value 형태로 이루어진 데이터 저장
     - 정렬된 순서(오름차순)로 저장
     - 저장시간이 다소 오래 걸림
   - LinkedHashMap
     - HashMap을 상속받기 때문에 매우 흡사
     - Map에 있는 엔트리들이 연결 리스트를 유지되므로 입력한 순서대로 반복 가능

   

   컬렉션 사용 시 데이터 정렬 방법

   - Comparable
   - Comparator