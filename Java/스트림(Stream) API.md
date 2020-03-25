## 자바 스트림(Stream) API

Stream은 Java8 부터 추가된 기능으로 배열 또는 컬렉션 인스턴스에 함수 여러 개를 조합해서 원하는 결과를 필터링하고 가공된 결과를 얻을 수 있는 기술 중 하나이다.

람다를 이용해서 코드의 양을 줄이고 간결하게 표현할 수 있다. 즉, 배열과 컬렉션을 함수형으로 처리할 수 있다.



##### 스트림에 대한 내용은 크게 세 가지로 나뉜다

1. 생성하기 : 스트림 인스턴스 생성(스트림 생성)

2. 가공하기 : 필터링(filtering) 및 매핑(mapping)등 원하는 결과를 만들어가는 **중간 작업**(중개연산)

3. 결과 만들기 : 최종적으로 결과를 만들어내는 작업(최종 연산)

   **객체.스트림 생성().중개연산().최종연산();**



### 생성하기

보통 배열과 컬렉션을 이용해서 스트림을 만들지만 그 외에도 다양한 방법으로 만들 수 있다.

##### 배열 스트림

스트림을 이용하기 위해서는 먼저 생성을 해야 한다. 스트림은 배열 또는 컬렉션 인스턴스를 이용해서 생성할 수 있다. 배열은 **Arrays.stream** 메소드를 사용한다. 

```
String[] arr = new String[] {"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);
```

##### 컬렉션 스트림

컬렉션 타입(Collection, List, Set)의 경우 인터페이스에 추가된 디폴트 메소드 stream을 이용해서 스트림을 만들 수 있다.

```
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();
```

##### Stream.builder()

빌터(Builder)를 사용하면 스트림에 직접적으로 원하는 값을 넣을 수 있다. 마지막에 build 메서드로 스트림을 리턴한다.

```

Stream<String> builderStream = 
	Stream.<String>builder()
		.add("BBB").add("AAA").add("CCC")
		.build(); // [BBB, AAA, CCC]
```

##### Stream.iterate()

**iterate** 메소드를 이용하면 초기값과 해당 값을 다루는 람다를 이용해서 스트림에 들어가 요소를 만든다.
아래 예제는 10이 초기값이고 2씩 증가하는 값들이 들어간다.
이 방법은 스트림의 사이즈가 무한이기 떄문에 특정 사이즈로 제한해야 한다

```
Stream<Integer> iteratedStream = 
	Stream.iterate(10, n -> n + 2).limit(5) ; // 10, 12, 14, 16, 18
```

##### 스트림 연결하기

**Stream.concat** 메서드를 이용해 두 개의 스트림을 연결해 새로운 스트림을 만들 수 있다.

```
Stream<String> stream1 = Stream.of("i", "am", "bang");
Stream<String> stream2 = Stream.of("you", "are", "hyun");
Stream<String> concatStream = Stream.concat(stream1, stream2);
// i, am, bang, you, are, hyun
```



### 가공하기

전체 요소 중에서 내가 원하는 것만 뽑아낼 수 있다. 여러 작업을 이어 붙여서 작성할 수 있다.

##### Filter

필터(filter)은 스트림 내 요소들을 하나씩 평가해서 걸러내는 작업으로 **조건에 맞는 것만 추출**해 준다.

```
Stream<String> stream = 
  names.stream()
  .filter(name -> name.contains("a"));
```

name로 스트림의 요소(names)를 받고 각 요소에 "a"라는 알파벳이 있는 것들만 추출해준다.

##### Mapping

맵(map)은 스트림 내 요소들을 하나씩 **특정 값으로 변환**해준다.
스트림에 들어가 있는 값이 input 이 되어서 특정 로직을 거친 후 output 이 되어(리턴) 새로운 스트림에 담기게 된다.

```Stream&lt;String&gt; stream = 
Stream<String> stream = 
  names.stream()
  .map(String::toUpperCase);
```

names에 있는 값들을 toUpperCase 메소드를 실행하여 대문자로 변환한 값들이 담긴 스트림을 리턴한다.

##### Sorting

말 그대로 스트림의 요소들을 정리해준다.

```
IntStream.of(14, 11, 20, 39, 23)
  .sorted()
  .collect(Collectors.toList());
  // 11, 14, 20, 23, 39
```

역순으로 정렬하고 싶으면 Comparator 를 사용하면 된다.

```
IntStream.of(14, 11, 20, 39, 23)
  .sorted(Comparator.reverseOrder())
  .collect(Collectors.toList());
  // 39, 24, 20, 14, 11
```

두 인자를 비교해서 값을 리턴하고 싶을 때는 compare 메소드를 사용하면 된다.

```
lang.stream() // 오름차순
  .sorted(Comparator.comparingInt(String::length))
  .collect(Collectors.toList());
// [Go, Java, Scala, Swift, Groovy, Python]

lang.stream() // 내림차순
  .sorted((s1, s2) -> s2.length() - s1.length())
  .collect(Collectors.toList());
// [Groovy, Python, Scala, Swift, Java, Go]
```



### 결과 만들기

가공한 스트림을 가지고 사용할 결과값을 만들어내는 단계이다. 따라서 스트림을 끝내는 최종 작업이라고 한다.

##### Calculating

count(), sum(), min(), max(), average() 메서드를 이용해 결과를 만들어낼 수 있다

```
 int count = IntStream.of(1, 2, 3, 4, 5).count(); // 5
 long sum = LongStream.of(1, 2, 3, 4, 5).sum(); // 15
```

##### Reduction

**reduce** 메서드를 이용해서 결과를 만들어낸다. 여러 요소의 종합을 낼 수 있다.

```
OptionalInt reduced = 
  IntStream.range(1, 4) // [1, 2, 3]
  .reduce((a, b) -> {
    return Integer.sum(a, b);
  });
  // 6
```

##### Collecting

**collect** 메소드는 Collector 타입의 인자를 받아서 처리를 한다. 스트림의 값들을 모아주는 기능으로, toMap, toSet, toList로 해당 스트림을 다시 컬렉션으로 바꿔준다

```
List<String> collectorCollection =
  productList.stream()
    .map(Product::getName) // Product(String name, int count)
    .collect(Collectors.toList());
    // Product 객체안에 있는 name 인스턴스를 반환
```

```
String listToString = 
 productList.stream()
  .map(Product::getName)
  .collect(Collectors.joining());
  /// name인스턴스를 하나로 이여붙인다
```

Collectors.**joining()** 은 스트림에서 작업한 결과를 하나의 스트링으로 이어 붙여준다.

##### Matching

매칭은 stream을 받아서 해당 조건을 만족하는 요소가 있는지 체크한 결과를 리턴한다.(boolean)

- anyMatch() : 하나라도 조건을 만족하는 요소가 있는지
- allMatch() : 모두 조건을 만족하는지
- noneMatch() : 모두 조건을 만족하지 않는지

```
List<String> names = Arrays.asList("Eric", "Elena", "Java");

boolean anyMatch = names.stream()
  .anyMatch(name -> name.contains("a")); // true
boolean allMatch = names.stream()
  .allMatch(name -> name.length() > 3); // true
boolean noneMatch = names.stream()
  .noneMatch(name -> name.endsWith("s")); // true
```