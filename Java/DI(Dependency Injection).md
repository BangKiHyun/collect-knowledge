## DI(Dependency Injection)

#### 의존관계(Dependency)란?

- 의존관계란 객체 혼자 모든 일을 처리하기 힘들기 대문에 내가해야 할 작업을 다른 객체에게 위임하면서 발생한다.

~~~
package DI;

import java.util.Calendar;

public class DateMessageProvider {
    public String getDateMessage() {
        Calendar now = Calendar.getInstance();
        int hour = now.get(Calendar.HOUR_OF_DAY);

        if (hour < 12) {
            return "오전";
        }
        return "오후";
    }
}
~~~

- 현재 시스템 시간에 따라 "오전" 또는 "오후"를 반환하는 DateMessageProvider 라는 클래스가 있다.
- getDateMessage() 에 대한 테스트 클래스는 다음과 같다.

~~~
public class DateMessageProviderTest {
    @Test
    public void 오전() throws Exception {
        DateMessageProvider provider = new DateMessageProvider();
        assertEquals("오전", provider.getDateMessage());
    }

    @Test
    public void 오후() throws Exception {
        DateMessageProvider provider = new DateMessageProvider();
        assertEquals("오후", provider.getDateMessage());
    }
}
~~~

- 위 테스트를 실행하면 컴퓨터의 현재 시간에 따라 테스트 메서드 중 둘 중 하나는 반드시 실패한다.
- 두 개의 테스트를 모두 성공하려면 Calender의 시간을 변경 할 수 있어야 하는데 변경할 방법이 없다. 소스코드를 컴파일하는 시점에 Calendar 인스턴스가 이미 결정되어 버리기 때문이다.
- 이와 같은 의존관계를 강하게 결합(tightly coupling)되어 있다고 한다.
- 테스트를 모두 성공하려면 Calendar에서 반환되는 시간을 변경할 수 있도록 Calendar 인스턴스에 대한 생성을 getDateMessage() 가 결정하는 것이 아니라 **DateMessageProvider 외부에서 Calendar 인스턴스를 생성한 후 전달하는 구조**로 바꿔야 한다.

외부에서 Calendar 인스턴스를 전달하는 방법을 두 가지 방법이 있다.

1. 생성자를 통해 전달
2. getDateMessage() 메서드 인자로 전달

두 번째 방법으로 getDateMessage() 메서드 인자로 전달하는 방법으로 리팩토링 하면 아래와 같다.

~~~
    public String getDateMessage(Calendar now){
        int hour = now.get(Calendar.HOUR_OF_DAY);
        
        if(hour < 12){
            return "오전";
        }
        return "오후";
    }
~~~

프로덕션 코드의 변경에 따라 테스트 코드는 다음과 같이 리팩토링한다.

~~~
public class DateMessageProviderTest {
    @Test
    public void 오전() throws Exception {
        DateMessageProvider provider = new DateMessageProvider();
        assertEquals("오전", provider.getDateMessage());
    }

    @Test
    public void 오후() throws Exception {
        DateMessageProvider provider = new DateMessageProvider();
        assertEquals("오후", provider.getDateMessage());
    }
    
    private Calendar createCurrentDate(int hourOfDay) {
        Calendar now = Calendar.getInstance();
        now.set(Calendar.HOUR_OF_DAY, hourOfDay);
        return now;
    }
}
~~~

- 이와 같이 DateMessageProvider가 의존하고 있는 Calendar 인스턴스의 생성을 외부로부터 전달받음으로써 테스트가 가능해졌다. 
- 이처럼 **객체 간의 의존관계에 대한 결정권을 외부의 누군가가 담당하도록 한 구조는 코드의 변화를 최소화하면서 확장하기도 쉽고, 테스트하기도 쉽게 된다**.

