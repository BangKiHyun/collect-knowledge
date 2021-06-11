# 프록시 패턴(Proxy Pattern)

## 프록시 패턴(Proxy Patter) 이란?

어떤 객체를 사용하고자 할 때, 객체를 직접적으로 참조하는 것이 아니라, 그 객체를 대행(대리, proxy)하는 객체를 통해 대상 객체에 접근하는 방식을 말한다.

즉, **실제 기능을 수행하는 객체(Real Object)대신 가상의 객체(Proxy Object)를 사용해 로직의 흐름을 제어하는 디자인 패턴이다.**

</br >

## 프록시 패턴 특징 및 장점

- 실제 대상 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정할 수 있다.
- 실제 객체의 기능이 반드시 필요한 시점까지 객체의 생성을 미룰 수 있다.
- 프록시 객체와 실제 객체는 같은 인터페이스를 구현하여 프록시 객체는 실제 객체와 치환이 가능하다.
- 사용자 입장에서는 프록시 객체와 실제 객체의 사용법이 유사하므로 사용성이 좋다.

## 프록시 패턴 단점

- 객체를 생성할 때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있다.
- 프록시 안에서 실제 객체를 생성하기 위해 thread가 생성되고 동기화가 구현돼야 하는 경우 성능이 저하되고 로직이 난해해질 수 있다.

</br >

## 프록시 패턴 클래스 다이어 그램

![img](https://blog.kakaocdn.net/dn/bhHF6T/btq64Saao66/4e91cmDExRru9KKxzQW2b0/img.png)

</br >

## 가상 프록시(Virtual Proxy)

가상 프록시는 실제 객체의 사용 시점을 제어할 수 있다. 지연 초기화(Lazy Initialization)를 사용해 가상 프록시를 구현해보자.

이미지 파일을 보여주는 인터페이스는 다음과 같다.

~~~java
public interface Image {
    void display();
}
~~~

실제 이미지를 보여주는 `RealImage`라는 클래스는 다음과 같다.

~~~java
public class RealImage implements Image{
    private String fileName;

    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk(fileName);
    }

    @Override
    public void display() {
        System.out.println("Displaying: " + fileName);
    }

    private void loadFromDisk(String fileName) {
        System.out.println("Loading: " + fileName);
    }
}
~~~

`RealImage`클래스는 디스크로부터 해당 파일 이름과 일치하는 파일을 로딩한다. 파일을 로딩하는데 일정 시간이 걸린다고 가정하자.

만약 100개의 파일을 생성할 때, 모든 파일들을 로딩해와야 하기 때문에 수행하는데 많은 시간이 걸릴 것이다. 프록시 패턴을 적용해 필요할 때만 파일을 로딩할 수 있도록 수정해보자.

~~~java
public class ProxyImage implements Image {
    private String fileName;
    private RealImage image;

    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }

    @Override
    public void display() {
        if (image == null) { // 실제 이미지가 없으면 loading (Lazy Loading)
            this.image = new RealImage(this.fileName);
        }
        image.display();
    }
}
~~~

</br >

### 결과 확인

```java
public class Client {

    public static void main(String[] args) {
        System.out.println("===== Real Image Test =====");
        System.out.println("===== 파일 생성과 동시에 로딩 =====");
        final Image realImage = new RealImage("real_image.jpg");

        System.out.println("\n===== Proxy Image Test =====");
        final Image proxyImage = new ProxyImage("proxy_image.jpg");
        System.out.println("===== display 호출 =====");
        proxyImage.display();
    }
}
```

![img](https://blog.kakaocdn.net/dn/bBaNkk/btq64MOCPDF/6yzTAlvcYGsSRHGaludwI1/img.png)

결과를 보면 `RealImage`는 파일 생성과 동시에 로딩하지만 `ProxyImage`는 필요 시점(display 호출)에 로딩한다.

이렇게 초기 비용이 많이 드는 연산이 포함된 객체의 경우 가상 프록시를 적용하면 큰 효과를 볼 수 있다.

</br >

## 보호 프록시(Protection Proxy)

보호 프록시는 프록시 객체가 사용자의 실제 객체에 대한 접근을 제어한다.

예를 들어 마스터, 매니저, 회원이 있다고 가정해보자. 각각의 직책에 따라 조회할 수 있는 정보는 다음과 같다.

- 마스터: 매니저 및 회원의 정보 조회 가능
- 매니저: 회원의 정보 조회 가능
- 회원: 자신의 정보만 조회 가능

따라서 직책에 따라 조회 가능한 정보의 범위를 제어해줘야 한다.

~~~java
public interface Employee {
    String getName();
    Grade getGrade();
    String getInformation(Employee viewer);
}
~~~

```java
public enum Grade {
    MASTER, MANAGER, USER
}
```

`Employee`인터페이스는 한 명의 구성원을 표현하고, `Grade`는 직책에 대한 정의를 나타낸다.

</br >

```java
public class NormalEmployee implements Employee {
    private String name;
    private Grade grade;

    public NormalEmployee(String name, Grade grade) {
        this.name = name;
        this.grade = grade;
    }

    @Override
    public String getName() {
        return this.name;
    }

    @Override
    public Grade getGrade() {
        return this.grade;
    }

    @Override
    public String getInformation(Employee viewer) {
        return "Information [Grade: " + getGrade() + ", Name: " + getName() +"]";
    }
}
```

`NormalEmployee`클래스는 단순히 `Employee`인터페이스를 구현했다. `getInformation`메서드를 통해 누구든 정보 조회가 가능하게 된다.

보호 프록시를 적용해 조회자의 직책에 따라 조회할 수 있는 정보의 범위를 제한해보자.

```java
public class ProtectedEmployee implements Employee {
    private Employee employee;

    public ProtectedEmployee(Employee employee) {
        this.employee = employee;
    }

    @Override
    public String getName() {
        return this.employee.getName();
    }

    @Override
    public Grade getGrade() {
        return this.employee.getGrade();
    }

    @Override
    public String getInformation(Employee viewer) {
        if (this.employee.getGrade() == viewer.getGrade() && this.employee.getName().equals(viewer.getName())) {
            return this.employee.getInformation(viewer);
        }

        switch (viewer.getGrade()) {
            case MASTER:
                if (this.employee.getGrade() == Grade.MANAGER || this.employee.getGrade() == Grade.USER) {
                    return this.employee.getInformation(viewer);
                }
            case MANAGER:
                if (this.employee.getGrade() == Grade.USER) {
                    return this.employee.getInformation(viewer);
                }
            case USER:
            default:
                throw new NotAuthorizedException();
        }
    }
}
```

위 코드를 보면 `getInformation`메서드 호출 시 조회자(viewer)에게 권한이 없으면 `NotAuthorizedException`예외를 던진다.

### 결과 확인

```java
public class Client {
    public static void main(String[] args) {
        Employee master = new NormalEmployee("마스터", Grade.MASTER);
        Employee manager = new NormalEmployee("매니저", Grade.MANAGER);
        Employee user = new NormalEmployee("회원", Grade.USER);
        final List<Employee> employeeList = Arrays.asList(master, manager, user);
      
        System.out.println("=================================");
        System.out.println("보호 프록시 서비스 가동");
        System.out.println("=================================");
        final List<Employee> protectedEmployeeList = employeeList.stream()
                .map(ProtectedEmployee::new)
                .collect(Collectors.toList());

        System.out.println("=================================");
        System.out.println("USER가 회원 정보 조회");
        System.out.println("=================================");
        findAllInformation(user, protectedEmployeeList);

        System.out.println("=================================");
        System.out.println("MANAGER가 회원 정보 조회");
        System.out.println("=================================");
        findAllInformation(manager, protectedEmployeeList);

        System.out.println("=================================");
        System.out.println("MASTER가 회원 정보 조회");
        System.out.println("=================================");
        findAllInformation(master, protectedEmployeeList);
    }

    private static void findAllInformation(Employee viewer, List<Employee> employeeList) {
        employeeList.stream()
                .map(employee -> {
                    try {
                        return employee.getInformation(viewer);
                    } catch (NotAuthorizedException e) {
                        return "Not authorized.";
                    }
                })
                .forEach(System.out::println);
    }
}
```

![img](https://blog.kakaocdn.net/dn/6xqEJ/btq63529fQk/hWzdseqFRfgYb9o6iljWZ1/img.png)

