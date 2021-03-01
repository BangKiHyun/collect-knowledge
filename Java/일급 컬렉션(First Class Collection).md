# 일급 컬렉션(First Class Collection)

예전에 스터디를 참여하면서 일급 컬렉션에 대해 공부하고, 실제로 적용해 본적이 있었습니다.

일급 컬렉션이 무엇인지, 어떤 장점이 있는지 지금부터 알아보겠습니다.

## 일급 컬렉션이란?

- 일급 컬렉션은 **Collection을 Wrapping 하면서, 그 외 다른 멤버 변수가 없는 상태**를 뜻합니다.
- 중요한 점은 Collection을 포함한 클래스는 **반드시 다른 멤버 변수가 없어야 한다**는 겁니다.

### 간단한 코드 예제

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(1);
numbers.add(2);
numbers.add(3);
```

위의 코드를 아래와 같이 Wrapping 하는 것을 얘기합니다.

```java
public class Numbers {
    private List<Integer> numbers;
    
    public FirstClass(List<Integer> numbers){
        this.numbers = numbers;
    }
}
```

</br >

## 일급 콜렉션의 장점

1. 비지니스에 종속적인 자료구조 입니다.
2. Collection의 불변성을 보장합니다.
3. 상태와 행위를 한 곳에서 관리합니다.
4. 이름이 있는 컬렉션입니다.

각각의 장점들을 하나 하나 알아보겠습니다.

</br >

### 1. 비지니스에 종속적인 자료구조

레이싱 게임을 한다고 가정하겠습니다.

레이싱 게임을 하기 위해서는 경주를 할 자동차들이 필요합니다. **자동차의 최대 갯수는 10개로 제한**합니다.

```java
public class RacingGame {
    private static final int MAX_SIZE = 10;

    public void raceStart() {
        List<Car> cars = createCars();
        validateSize(cars);
        // race logic start
    }

    private void validateSize(List<Car> cars) {
        if (cars.size() > MAX_SIZE) {
            throw new IllegalArgumentException(String.format("Exceeded maximum number of cars. input cars size: %d", cars.size()));
        }
    }

    private List<Car> createCars() {
        // createCars
    }
}
```

위 코드를 보면 RacingGame 클래스에서 race에 관련된 로직 뿐만 아니라 자동차에 관한 검증로직까지 처리합니다. 이렇게 된다면 **자동차를 사용하는 모든 메서드에서 검증로직을 추가**해야 합니다.

이 문제를 해결하기 위해서 해당 조건을 만족하는 객체를 만들어주면 됩니다. 이게 바로 일급 컬렉션입니다.

~~~java
// 일급 컬렉션
public class Cars {

    private final int MAX_SIZE = 10;
    private final List<Car> cars;

    public Cars(List<Car> cars) {
        validateCount(cars);
        this.cars = cars;
    }

    private void validateCount(List<Car> cars) {
        if (cars.size() > MAX_SIZE) {
            throw new IllegalArgumentException(String.format("Exceeded maximum number of cars. input cars size: %d", cars.size()));
        }
    }
}
~~~

```java
public class RacingGame {
    
    public void startRace() {
        final Cars cars = new Cars(createCars());
        // race logic start
    }
  
    private List<Car> createCars() {
        // createCars
    }
}
```

이처럼 Car 관련 비지니스 로직(여기서는 최대 갯수)을 Cars라는 일급 컬렉션에서 해결할 수 있는, 비즈니스에 종속적이 자료구조가 만들어 졌습니다.

</br >

### 2. Collection의 불변성을 보장

일급 컬렉션은 컬렉션의 불변성을 보장해 줍니다. 그렇다면 `final`과의 차이점은 무엇일까요?

- `final`은 단순히 **재할당만 금지할 뿐, 구조적 변경은 허용합니다.**(add, remove 허용)
- 일급 컬렉션은 단순히 `final` 을 사용하는 것이 아니라 캡슐화를 통해 이뤄지기 때문에 구조적 변경이 불가능합니다.
  - 구조적 변경의 허용 유무는 불변 객체를 만드는데 매우 중요합니다.
  - 불변 객체를 만들기 위해서는 **해당 객체의 값을 변경할 수 있는 메서드가 없어야 하기 때문입니다.**

```java
public class Cars {

    private final List<Car> cars;

    public Cars(List<Car> cars) {
        this.cars = cars;
    }

    public int getTotalPrice() {
        return cars.stream()
                .mapToInt(Car::getPrice)
                .sum();
    }
}

```

위 코드는 일급 컬렉션 입니다. **이 코드를 보면 구조적 변경을 할 수 없고, 내부 값도 변경할 수 있는 메서드는 존재하지 않습니다.**

</br >

### 3. 상태와 행위를 한곳에서 관리

일급 컬렉션은 **값과 로직이 함께 존재**합니다. 즉, 응집도가 높다는걸 의미합니다.

예를 들어 여러 Car들이 모여있고, 이 중 우리나라에서 생성한 Car의 갯수를 얻어야 한다고 가정해보겠습니다.

```java
public class Cars {

    private final List<Car> cars;

    public Cars(List<Car> cars) {
        this.cars = cars;
    }

    public Long getKrCarCount() {
        return cars.stream()
                .filter(car -> car.isKrCar())
                .count();
    }
}

```

위와 같이 Cars 라는 일급 컬렉션을 만들고, 그 안에 우리나라의 Car 갯수를 가져올 수 있는 코드를 구현했습니다.

즉, 일급 컬렉션을 사용함으로써 상태와 로직을 한곳에서 관리할 수 있게 됐습니다.

</br >

### 4. 이름이 있는 컬렉션

예를 들어 한국 자동차 그룹과 미국 자동차 그룹을 구분해야 한다고 가정해보겠습니다.

다음과 같이 변수명을 다르게 하여 구분했습니다.

~~~java
List<Car> KrCars = createKrCars();
List<Car> UsaCars = createUsaCars();
~~~

위 코드의 문제점은 다음과 같습니다.

- 한국 자동차의 그룹이라는 뜻은 **개발자마다 다르게 지을 수** 있습니다. 그렇기 때문에 변수명으로 유추한 그룹이 원하는 그룹이 아닐수도 있습니다.
- 변수명에 불과하기 때문에 **의미를 부여하기 어렵습니다.**
- 중요한 값임에도 이를 표현할 **명확한 단어가 없습니다.**

위 문제을 해결책으로 각각의 그룹을 일급 컬렉션으로 만들면 됩니다.

```java
KrCars krCars = new KrCars(createKrCars());
UsaCars usaCars = new UsaCars(createUsaCars());
```

위와 같이 한국자동차 와 미국자동차 **각각의 일급 컬렉션을 만들어서** 해당 문제점들을 해결할 수 있습니다.

</br >

## 참조

<https://jojoldu.tistory.com/412>