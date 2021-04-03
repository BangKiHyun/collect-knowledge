# 플라이웨이트 패턴(Flyweight Pattern)

## 플라이웨이트 패턴이란?

플라이웨이트 패턴은 구조 패턴 중 하나로, 많은 수의 객체를 생성해야할 때 사용하는 패턴입니다.

플라이웨이트 패턴을 사용하면 객체의 내부에서 참조하는 객체를 직접 만드는 것이 아니라, **없다면 만들고, 만들어져 있다면 객체를 공유하는 식으로 객체를 구성**합니다.

보통 팩토리 메서드 패턴을 사용해 객체(Flyweight 객체)를 생성하며, 이때 생성하는 객체가 내부적으로 참조하는 객체에 대해, 기존에 있는 객체를 참조만 하는 식으로 객체를 구성하기 때문에 **객체의 할당에 사용되는 메모리를 줄일 수 있을 뿐 아니라, 객체를 생성하는 시간도 들지 않게 됩니다.**

</br >

## 플라이웨이트 패턴을 언제 적용하면 좋을까?

플라이웨이트 패턴은 다음과 같은 상황이 많을수록 적용하기에 적합합니다.

- 애플리케이션에 의해 생성되는 객체의 수가 많다.
- 생성된 객체가 오래도록 메모리에 상주하며, 사용되는 횟수가 많다.
- 객체의 속성을 공유 속성과 그렇지 않은 속성으로 나눌 수 있다.

플라이웨이트 패턴의 가장 대표적인 예로 자바의 `String pool`이 있습니다. 컴파일 타이밍에 Sring 객체로 선언되어있는 것들은 jvm heap 메모리의 permernant 영역으로 들어가게 됩니다.

만약 같은 내용의 `String` 객체가 선언될 때 새로운 메모리를 할당하는 것이 아니라 `Spring pool`에 있는지 검사 후 있으면 가져오고 없으면 새로 메모리에 할당하여 사용하도록 하고 있습니다.

</br >

## 플라이웨이트 패턴 예제

게임상에서 새를 표현할 때 기본적인 위치정보와 함께, 유닛의 체력(physical), 공격력(damage)들의 요소를 가지고 구성할 수 있습니다.

그럴때 각각의 유닛에 대해 physical, damage, 위치정보에 대한 객체를 가지고 있다고 표현할 수 있습니다.

~~~java
public class Unit {
    private Physical physical;
    private Damage damage;
    private Position position;
}
~~~

위 방식에서 해당 유닛에 대한 공통 속성으로 physical과 damage가 있습니다. 이 속성을 공유하는 방식으로 객체를 다시 구성할 수 있습니다. 참고로 여기서 공유되는 속성을 특정 인스턴스에게만 다르게 적용하는 것은 불가능합니다.

~~~java
// 공유할 객체를 감싼 모델 정의
public class UnitModel {
    private Physical physical;
    private Damage damage;

    public UnitModel() {
        this.physical = new Physical(40);
        this.damage = new Damage(6);
    }
}

// 실제로 매번 생성할 유닛 클래스
public class Unit {
    private UnitModel unitModel;
    private Position position;

    public Unit(UnitModel unitModel, Position position) {
        this.unitModel = unitModel;
        this.position = position;
    }
}
~~~

다음은 유닛에 대한 플라이웨이트 팩토리 클래스를 작성할 수 있습니다.

~~~java
public class UnitFactory {
    private static UnitModel sharedUnitModel;

    private UnitFactory() {
    }

    public static Unit create(Position position) {
        if (sharedUnitModel == null) {
            sharedUnitModel = new UnitModel();
        }
        return new Unit(sharedUnitModel, position);
    }
}
~~~

