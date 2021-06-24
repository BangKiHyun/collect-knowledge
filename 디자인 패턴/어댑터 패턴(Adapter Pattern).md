# 어댑터 패턴(Adapter Pattern)

## 어댑터 패턴이란?

어댑터 패턴은 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있도록 도와주는 패턴이다.

즉, 어댑터를 이용해 한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환시켜 호환성 문제를 해결한다.

</br >

예를 들어, 갤럭시 핸드폰을 충전해야 하는데 현재 아이폰 충전기밖에 없다면 어댑터를 사용해서 아이폰 충전기로도 갤럭시 핸드폰을 충전할 수 있다. 여기서의 어댑터는 핸드폰을 충전할 때 필요한 인터페이스를 각각의 타입이 필요로 하는 인터페이스로 바꿔준다고 할 수 있다.

</br >

## 코드 예제

### 시나리오

현재 `GalaxyPhone`을 사용하고 있다고 가정해보자. 핸드폰을 충전하기 위해 `charging()`메서드를 호출하는데, 이 처리는 `PhoneCharger`인터페이스를 구현한 `GalaxyPhoneCharger`에게 위임하도록 되어있다. 이때, `PhoneCharger`인터페이스를 구현한 `GalaxyPhoneCharger`의 `charging()`메서드를 호출한다.

하지만 현재 아이폰 충전기밖에 없어 `IPhoneCharger`로 변경해야 하는 상황이 생겼다. 이때 어댑터 패턴을 적용하여 기존의 코드를 수정하지 않고 `IPhoneCharger`를 적용할 수 있다.

</br >

`PhoneCharger.java`

```java
public interface PhoneCharger {
    void charging();
}
```

</br >

`GalaxyPhoneCharger.java`

```java
public class GalaxyPhoneCharger implements PhoneCharger{
    @Override
    public void charging() {
        System.out.println("charging phone using the GalaxyPhone Charger");
    }
}
```

</br >

`IPhoneCharger.java`

```java
public class IPhoneCharger {
    public void charging() {
        System.out.println("charging phone using the IPhone Charger");
    }
}
```

`IPhoneCharger`는 따로 어댑터 패턴의 이해를 돕기위해 따로 `PhoneCharger`인터페이스를 상속받지 않았다.

</br >

`GalaxyPhone.java`

```java
public class GalaxyPhone implements Phone {

    private final PhoneCharger charger;

    public GalaxyPhone(PhoneCharger charger) {
        this.charger = charger;
    }

    @Override
    public void charging() {
        charger.charging();
    }
}
```

`GalaxyPhone`은 `PhoneCharger`를 주입받는다. `charging()`을 호출하면 `PhoneCharger`인터페이스를 구현한 클래스의 `charging()`메서드를 호출한다.

</br >

`IPhoneChargerAdapter.java`

```java
public class IPhoneChargerAdapter implements PhoneCharger {

    private final IPhoneCharger IPhoneCharger;

    public IPhoneChargerAdapter(IPhoneCharger IPhoneCharger) {
        this.IPhoneCharger = IPhoneCharger;
    }

    @Override
    public void charging() {
        IPhoneCharger.charging();
    }
}
```

`IPhoneChargerAdapter`는 `PhoneCharger`인터페이스를 구현한다. 이때 `IPhoneCharger`를 주입받아 `IPhoneCharger`의 `charging()`메서드를 호출한다.

</br >

### Test

```java
public class Main {

    public static void main(String[] args) {
        final IPhoneChargerAdapter adapter = new IPhoneChargerAdapter(new IPhoneCharger());
        final GalaxyPhone galaxyPhone = new GalaxyPhone(adapter);
        galaxyPhone.charging();
    }
}
```

![img](https://blog.kakaocdn.net/dn/cUnnRp/btq72iGpDKX/k2u7CSAqMkE30r5cZEQSXk/img.png)

실행 결과를 보면 `IPhoneCharger`가 실행된 것을 확인할 수 있다.

</br >

위 코드의 구조는 다음과 같다.

![img](https://blog.kakaocdn.net/dn/cGC9Rq/btq70CSX5wj/IKslyk5vSu1mT6SVgyRFJk/img.png)

Client에서 Target Interface를 호출하는 것처럼 보이지만. Client의 요청을 전달받은 Adapter(Target Intercafe 구현)는 자신이 감싸고 있는 Adaptee에게 실질적인 처리를 위임한다.

그렇기 때문에 기존 코드를 수정하지 않고도 `IPhoneCharger`를 적용할 수 있다.

