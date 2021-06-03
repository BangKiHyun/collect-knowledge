# 상태 패턴(State Pattern)

## 상태 패턴이란?

상태 패턴은 한 객체에게 여러개의 상태(State)가 존재하고, **상태간에 긴밀한 연결성이 있을 경우 상태간의 전이를 용이하게 해주는 패턴이다**.

상태 패턴에서 객체는 최초의 상태객체를 입력받고, 외부 입력에 따라 다른 상태객체로 변경되는데, 이때 두 번째 상태객체는 첫 번째 상태객체가 생성한다.

전략패턴이 여러개의 전략객체를 상황에 따라 주체객체에 직접 연결하는 것과 달리, **상태 패턴에서는 상태객체가 다음 상태객체를 생성하고, 현재 상태객체로 설정한다.**

예를 들어 핸드폰에 세가지 상태가 있다고 가정해보자.

- 전원 OFF
- 전원 ON, 화면 ON
- 전원 ON, 화면 OFF

핸드폰에 전원 버튼이 있는데 이걸 누를 때 다음과 같은 상태의 변이가 일어나게 된다.

1. 전원이 OFF일 때 전원 버튼을 누르면 전원이 켜지고, 화면이 나온다. (전원 OFF -> 전원 ON, 화면 ON)
2. 전원이 ON이고, 화면이 켜져있을 때 전원 버튼을 누르면 화면만 꺼진다. (전원 ON & 화면 ON -> 전원 ON & 화면 OFF)
3. 전원이 ON이고, 화면이 꺼져있을 때 전원 버튼을 누르면 화면만 켜진다. (전원 ON & 화면 OFF -> 전원 ON & 화면 ON)

</br >

## 일반적인 if else문을 사용한 코드

~~~java
public class Phone {
    private static final String POWER_OFF = "power off";
    private static final String POWER_ON_DISPLAY_ON = "power on display on";
    private static final String POWER_ON_DISPLAY_OFF = "power on display off";

    private String state;

    public Phone() {
        this.state = POWER_OFF; //초기 상태 설정 (전원 Off)
    }

    public void pressPowerButton() {
        if (this.state.equals(POWER_OFF)) {
            changeState(POWER_ON_DISPLAY_ON);
        } else if (this.state.equals(POWER_ON_DISPLAY_ON)) {
            changeState(POWER_ON_DISPLAY_OFF);
        } else if (this.state.equals(POWER_ON_DISPLAY_OFF)) {
            changeState(POWER_ON_DISPLAY_ON);
        }
    }

    public void changeState(String state) {
        this.state = state;
    }
}
~~~

위 코드는 하나의 행위에 대해 모든 상태를 일일이 if문을 만들어 상태변수를 변경해줘야 한다.

상태가 많아질수록 코드가 복잡해지고, 디버깅이 혼란스러워 질 수 있다.

</br >

## 상태 패턴 적용 코드

### PhoneState 인터페이스(행위주체 객체에 대한 설정 메서드 정의)

~~~java
public interface PhoneState {
    void pressPowerButton(Phone phone);
}
~~~

</br >

### PowerOffState 클래스

```java
public class PowerOffState implements PhoneState {
    private static PhoneState powerOffState = new PowerOffState(); //싱글턴

    private PowerOffState() { //객체 생성 방지
    }

    public static PhoneState getInstance() {
        return powerOffState;
    }

    @Override
    public void pressPowerButton(Phone phone) {
        phone.changeState(PowerOnDisplayOnState.getInstance());
        System.out.println("Power Off");
    }
}
```

</br >

### PowerOnDisplayOnState 클래스

```java
public class PowerOnDisplayOnState implements PhoneState {
    private static PhoneState powerOnDisplayOnState = new PowerOnDisplayOnState();

    private PowerOnDisplayOnState() {
    }

    public static PhoneState getInstance() {
        return powerOnDisplayOnState;
    }

    @Override
    public void pressPowerButton(Phone phone) {
        phone.changeState(PowerOnDisplayOffState.getInstance());
        System.out.println("Power On & Display On");
    }
}
```

</br >

### PowerOnDisplayOffState 클래스

```java
public class PowerOnDisplayOffState implements PhoneState {
    private static PhoneState powerOnDisplayOffState = new PowerOnDisplayOffState();

    private PowerOnDisplayOffState() {
    }

    public static PhoneState getInstance() {
        return powerOnDisplayOffState;
    }

    @Override
    public void pressPowerButton(Phone phone) {
        phone.changeState(PowerOnDisplayOnState.getInstance());
        System.out.println("Power On & Display Off");
    }
}
```

</br >

### Phone 클래스

~~~java
public class Phone {
    private PhoneState state;

    public Phone() {
        this.state = PowerOffState.getInstance(); //초기 상태 설정 (전원 Off)
    }

    public void pressPowerButton() {
        this.state.pressPowerButton(this);
    }

    public void changeState(PhoneState newPhoneState) {
        this.state = newPhoneState;
    }
}
~~~

위 코드는 각각의 상태를 클래스로 분리하고, 각자에 맞는 방식으로 로직을 구현해서 실행한다.

</br >

### 실행 결과

```java
public class Client {
    public static void main(String[] args) {
        final Phone phone = new Phone();

        for (int count = 0; count < 5; count++) { //5번 실행
            phone.pressPowerButton();
        }
    }
}
```

![img](https://blog.kakaocdn.net/dn/L2iWm/btq6uIzIbGo/kmLnKzVRBCghrjtXJiH5k1/img.png)

1. 처음 버튼을 눌렀을때 PowerOff의 상태가 실행된다.
2. 이후 화면 켜짐과 꺼짐이 반복된다.

실행 결과를 봤을때 처음에 가정한 대로 코드가 잘 돌아가는 것을 확인할 수 있다.

</br >

## 상태 패턴의 장단점

### 장점

- 복잡한 상태간의 전이에 대해 쉽게 기술하고 확장할 수 있다.

### 단점

- 클래스의 갯수가 많아질 수 있다.

