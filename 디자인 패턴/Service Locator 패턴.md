# Service Locator 패턴

## Service Locator 패턴이란?

Service Locator는 의존성을 해결할 객체들을 보관하는 일종의 저장소입니다.

외부에서 객체에게 의존성을 전달하는 주입과 달리 Service Locator에게 **직접 의존성을 해결해줄 것을 요청**합니다.

</br >

## 영화 예매 코드 예제

### ServiceLocator.class

~~~java
public class ServiceLocator{
		private static ServiceLocator saleInstance = new ServiceLocator();
		private DiscountPolicy discountPolicy;
		
		public static DiscountPolicy discountPolicy() { //인스턴스를 반환하기 위한 메서드
				return saleInstance.discountPolicy;
		}
		
		public static void provide(DiscountPolicy discountPolicy){ //인스턴스를 등록하기 위한 메서드
				saleInstance.discountPolicy = discountPolicy;
		}
		
		private ServiceLocator() { //싱글턴을 보장을 위한 인스턴스 생성 방지
		}
}
~~~

### Movie.class

~~~java
public class Movie {
		...
		private DiscountPolicy discountPolicy;
		
		public Movie(String title, Duration runningTime, Money fee) {
				...
				this.discountPolicy = ServiceLocator.discountPolicy();
		}
}
~~~

</br >

### 사용 방법

예를 들어, 영화를 예매하기 위한 할인 정책이 있다고 가정해보겠습니다. 할인 정책에는 금액 할인과 퍼센티지 할인이 있습니다.

두 가지 할인 정책중 금액 할인(AmountDiscount)을 적용하고 싶습니다. 이는 Movie의 인스턴스가 AmountDiscountPolicy의 인스턴스에 의존하기를 원한다고 표현할 수 있고, 다음과 같이 생성하면 됩니다.

~~~java
ServiceLocator.provide(new AmountDiscountPolicy(...));
Movie avatar = new Movie(...);
~~~

provide 메서드로 등록한 인스턴스(AmountDiscountPolicy)는 **이후에 생성되는 모든 객체(Movie)에게 적용이 됩니다.**

Movie는 DiscountPolicy의 구체 클래스를 모를 뿐만 아니라 의존성 문제까지 해결할 수 있습니다. 하지만 Service Locator 패턴에는 문제점이 하나 존재합니다.

</br >

## Service Locator의 숨겨진 의존성 문제점

위 코드를 보면 Movie는 DiscountPolicy에 의존하고 있지만 **Movie의 퍼블릭 인터페이스에 어디에도 DiscountPolicy 의존성에 대한 정보가 없습니다.** 이와 같이 Service Locator 패턴에는 의존성을 감추는 문제점이 있습니다.

</br >

### 코드 예제

ServiceLocator.provide 메서드에 인스턴스를 주입하지 않았다고 가정하고 Movie를 생성해 보겠습니다.

~~~java
Movie avatar = new Movie("아바타",
        Duration.ofMinutes(120),
		    Money.wons(10000));
~~~

위와 같이 Movie를 온전한 상태로 생성한 다음 할인 관련 코드가 실행될 때 `NullPointerException`이 발생합니다.

위 문제가 발생하는 이유는 Movie의 퍼블릭 인터페이스에 DiscountPolicy에 의존한다는 정보가 없기 때문입니다. 이것이 **숨겨진 의존성의 문제**입니다.

</br >

## 숨겨진 의존성의 단점

- 의존성을 구현 내부로 감출 경우 **의존성과 관련된 문제가 컴파일타임이 아닌 런타임에 가서야 발견됩니다.**
  - 즉 코드 작성 시점이 아닌 실행 시점으로 미룹니다.

- 숨겨진 의존성은 결과적으로 **내부 구현을 이해할 것을 강요합니다.**
  - Movie가 DiscountPolicy를 의존하고 있다는 것을 알기 위해서는 코드의 내부 구현을 파헤쳐 봐야 합니다.
- 단위 테스트 작성이 어렵습니다.
  - 단위 테스트 프레임워크는 테스트 케이스 단위로 **테스트에 사용될 객체들을 새로 생성하는 기능을 제공**합니다. 하지만 위에서 구현한 ServiceLocator는 정적 변수를 사용하기 때문에 상태를 공유하게 됩니다.
  - 즉, 각 단위 테스트는 서로 고립돼야 한다는 기본 원칙을 위반합니다.