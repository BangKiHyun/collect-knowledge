**1. Stack**

> \- Heap 영역에 생성된 Object 타입의 데이터의 **참조값**이 할당된다
>
> \- **원시타입(primitive type)의 데이터가 값과 함께 할당**된다
>   이때 원시타입의 데이터들에 대해서는 참조값을 저장하는 것이 아니라 **실제 값을 stack에 직접 저장**한다
>
> \- Thread는 자신만의 stack을 가진다
>   **Stack 메모리는 Thread하나당 하나씩 할당**된다.
>   즉, 스레드 하나가 새롭게 생성되는 순간 해당 스레드를 위한 stack도 함께 생성되며,
>   다른 스레드의 stack영역에 접근할 수 없다



**2. Heap**

> \- Heap영역에는 주로 긴 생명주기를 가지는 데이터들이 저장된다
>
> \- stack에 있는 데이터를 제외한 부분이다
>
> \- 모든 **Object 타입(참조형)은 heap영역에 생성**된다
>
> \- 몇개의 스레드가 존재하든 상관없이 단하나의 heap영역만 존재한다
>
> \- Heap영역에 있는 오브젝트들을 가리키는 레퍼런스 변수가 stack에 올라가게 된다



 ex) 코드예제

 public class Main {

​    public static void main(String[] args) {

​        int port = 4000;

​        String host = "localhost";

​     }

 }

**int port = 4000**은 기본형으로 stack에 4000이라는 값이 port라는 변수명으로 할당된다.

String은 Object를 상속받아 구현되었으므로 heap영역에 할당되고 stack에 host라는 이름으로 생성된 변수는 

heap에 있는 "localhost"라는 스트링을 레퍼런스 하게 된다. 그림으로 표현하면 아래와 같다.



![img](https://cafefiles.pstatic.net/MjAxOTEyMDNfMTQ2/MDAxNTc1MzYwMzUxMzUw.J9gpiVq_oVqnGP1lXYDa9Q8LP91YF8R8qHHZS8Rr5wkg.aedh6LbT0SE2wX6yvCQwryDt1juKA66-PtScemT7KfUg.PNG/1.PNG)