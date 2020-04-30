## Enum

### Enum class란

- Enum은 열거형이라고 불리며, **서로 연관된 상수들의 집한**을 의미한다.

- final static String 과 같이 문자열이나 숫자들을 나타내는 기본자료형의 값을 enum을 이용해서 같은 효과를 낼  수 있다.



### Enum의 장점

1. 코드가 단순해지며, 가독성이 좋다.
2. **인스턴스 생성과 상속을 방지**하여 상수값의 타입안정성이 보장된다.
3. enum class를 사용해 새로운 상수들의 타입을 정의함으로 **정의한 타입이외의 타입을 가진 데이터값을 컴파일시 체크**한다.
4. 키워드 enum을 사용하기 때문에 **구현의 의도가 열거임**을 분명하게 알 수 있다.



### Enum의 특징

1. 열거형으로 선언된 순서에 따라 0부터 인덱스 값을 가진다. 순차적으로 증가된다.

2. enum 열거형으로 지정된 상수들은 모두 대문자로 선언해야한다.

3. 열거형 변수들을 선언한 후 마지막 변수는 세미콜론(;)은 찍지 않는다.
   하지만, 상수와 연관된 문자를 연결시킬 경우 세미콜론(;)을 찍는다.

   ex) PLUS("+"), MINUS("-"), MULTIPLE("*"), DIVIDE("/");



### Enum class 사용법

```
public enum OperationType{
    PLUS, MINUS, MULTIPLE, DIVIDE
}

public class Calculator{
	private int firstOperand;
	private int secondOperand;
	private OperationType type;

    public static void main(String[] args){
        Calculator calculator = new Calculator();
        
        calculator.firstOperand = 1;
        calculator.secondOperand = 2;
        calculator.type = OperationType.PLUS;
        
        System.out.println("첫 번째 숫자 : " + calculator.firstOperand);
        System.out.println("두 번째 숫자 : " + calculator.secondOperand);
        System.out.println("수행할 연산 : " + calculator.type);
    }
}

//출력 결과
//첫 번째 숫자 : 1
//두 번째 숫자 : 2
//수행할 연산 : PLUS
```



### Enum static 메소드

|      메소드 이름       |                       설명                       |
| :--------------------: | :----------------------------------------------: |
|    valueOf(String)     |           String 값을 enum에서 가져옴            |
| valueOf(Class, Strnig) | 넘겨받은 class에서 String값을 찾아 enum에 가져옴 |
|        values()        | enum의 요소들을 순서대로 enum타입의 배열로 리턴  |



1. ##### valueOf(String) : 매개변수로 주어진 String과 열거형에서 일치하는 이름을 갖는 원소를 반환

   ```
   public enum OperationType{
       PLUS, MINUS, MULTIPLE, DIVIDE
   }
   
   public class Calculator{
   
   	public static void main(String[] args){
           Calculator calculator = new Calculator();
           
           OperationType type1 = OperationType.PLUS;
           OperationType type2 = OperationType.valueOf("MINUS");
           
           System.out.println(type1);
           System.out.println(type2);
       }
   }
   
   //출력 결과
   //PLUS
   //MINUS
   ```

2. values() : 열거된 모든 원소를 배열에 담아 순서대로 리턴

   ```
   public enum OperationType{
       PLUS, MINUS, MULTIPLE, DIVIDE
   }
   
   public class Calculator{
   
       public static void main(String[] args){
       	for(OperationType type : OperationType.values()){
           	System.out.println(type);            
       	}
       }
   }
   
   //출력 결과
   //PLUS
   //MINUS
   //MULTIPLE
   //DIVIDE
   ```

   

아래 사이트는 Enum class의 각각의 메소드 활용법에 대해 자세히 알려주고있다!
<https://www.baeldung.com/java-enum-values>