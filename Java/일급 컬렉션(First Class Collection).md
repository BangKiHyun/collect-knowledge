## 일급 컬렉션(First Class Collection)

>일급 컬렉션 사용
>
>- 컬렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다.
>- 즉, Collection을 Wrapping 하면서, 그 외 다른 멤버 변수가 없는 상태를 뜻한다.

예를 들어

```
List<String> text = new ArrayList<>();
text.add("1");
text.add("2");
text.add("3");
```

위의 코드를 아래와 같이 Wrapping 하는 것을 얘기한다.

```
public class FirstClass{
    private List<String> text;
    
    public FirstClass(List<String> text){
        this.text = text;
    }
}
```



### 일급 콜렉션의 이점

1. 비지니스에 종속적인 자료구조
2. Collection의 불변성을 보장
3. 상태와 행위를 한 곳에서 관리
4. 이름이 있는 컬렉션



### 비지니스에 종속적인 자료구조

```
public class User {
  private CarBundle carBundle;
}

//일급 컬렉션!!
public class CarBundle {
  private final int MAX_COUNT = 10;
  private List<Car> cars;
  
  private void addCar(Car car){
	if(cars.size() < MAX_COUNT){
       carBundle.add(car); 
	}
  }
}

public class Car{
    private String name;
    private String color;
}
```

이처럼 Car 관련 비지니스 로직(여기서는 최대 갯수)을 CarBundle 에서만 관리하게 됨으로써
**비지니스에 종속적인 자료구조**가 만들어져, User 와 Car 에 대한 응집도를 낮출 수 있습니다.



### Collection의 불변성을 보장

일급 컬렉션을 컬렌션의 불변성을 보장하는데, 단순히 **final** 을 사용하는 것이 아니라 캡슐화를 통해 이뤄진다.
**final** 은 재할당만 금지할 뿐이다.

그래서 컬렉션의 값을 변경할 수 있는 메소드가 없는 컬렉션을 만들면 불변 컬렉션이 된다.
즉, CarBundle 클래스에서 생성자와 **getter 외에 다른 메소드가 있으면 안된다**.

```
public class CarBundle {
    private final List<Car> cars;
    
    public CarBundle(List<Car> cars){
        this.cars = cars;
    }
    
    public CarBundle getCar(){
        return cars.get(0);
    }
}
```



### 상태와 행위를 한곳에서 관리

일급 컬렉션은 **값과 로직이 함께 존재**한다. 즉, 응집도가 높다는걸 뜻한다.

예를 들어 여러 Car들이 모여있고, 특정 Car의 움직인 거리가 필요하다고 가정하자.

```
public class CarBundle {
    private CarBundle cars;
    
    public CarBundle(List<Car> cars){
        this.cars = cars;
    } 
    
    public int getCarMoveDistance() {
        return cars.stream()
        		.filter(car -> CarType.isCarType(car.getCarType)))
        		.mapToInt(Car::getAmount)
        		.sum();
    }
}
```

이렇게 CarBundle 라는 일급 컬렉션이 생김으로 Car의 **상태와 로직이 한곳에서 관리**된다.



### 이름이 있는 컬렉션

예를 들어, Korea Cars에 대한 요구사항을 검색하거나 선언할 경우 아래와 같은 문제점을 겪을 수 있다.

- 국가마다 변수명이 다르다.
- 중요한 값이지만 명확하게 표현해둔 단어/변수명이 없다.

일급 컬렉션을 사용하면 Korea Car에 대한 요구사항이 바뀌었을 경우 KoreaEmails 만 검색하면 된다.

```
List<Car> KrCars = createKrCars();
List<Car> UsaCars = createUsaCars();
```

위 코드의 문제점은

1. 한국자동차 그룹이 어떻게 사용되는지 **검색 시 변수명으로만 검색**할 수 있다.
2. 한국자동차의 그룹이라는 뜻은 **개발자마다 다르게 지을 수** 있다.
3. 변수명에 불과하기 때문에 **의미를 부여하기 어렵다**.
4. 중요한 값임에도 이를 표현할 **명확한 단어가 없다**.

```
KrCars krCars = new KrCars(createKrCars());
UsaCars usaCars = new UsaCars(createUsaCars());
```

위와 같이 한국자동차 와 미국자동차 **각각의 일급 컬렉션을 만들어서** 해결할 수 있다.



더 자세한 정보는 아래 글을 참고하자!!
<https://jojoldu.tistory.com/412>