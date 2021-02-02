# 전략패턴(Strategy Pattern)

## 전략 패턴(Strategy Pattern)이란?

전략 패턴이란 객체들이 할 수 있는 행위 각각에 대해 전략 클래스를 생성하고, **유사한 행위들을 캡슐화하는 인터페이스를 정의**하여 객체의 행위를 동적으로 바꾸고 싶은 경우 직접 행위를 수정하지 않고 전략을 바꿔주기만 함으로써 행위를 유연하게 확장하는 방법입니다.

즉, 객체가 할 수 있는 행위들 각각을 전략으로 만들어 놓고, 동적으로 행위의 수정이 필요한 경우 전략을 바꾸는 것만으로도 행위의 수정이 가능하도록 만든 패턴이라고 볼 수 있습니다.

</br >

## 전략 패턴 예제(계산기)

1. 계산을 하는 방식은 +, -, *, / 총 4가지 방식이 있습니다.

2. 유사한 행위들을 캡슐화하는 인터페이스를 정의하겠습니다.

   ~~~java
   public interface OperationStrategy {
       int calculate(int firstOperand, int secondOperand);
   }
   ~~~

3. 다음으로 각각에 대해 전략 클래스를 생성하겠습니다.

   ~~~java
   //Plus
   public class PlusOperation implements OperationStrategy {
       @Override
       public double calculate(double fistOperand, double secondOperand) {
           return fistOperand + secondOperand;
       }
   }
   
   //Minus
   public class MinusOperation implements OperationStrategy {
       @Override
       public double calculate(double fistOperand, double secondOperand) {
           return fistOperand - secondOperand;
       }
   }
   
   //Multiple
   public class MultipleOperation implements OperationStrategy {
       @Override
       public double calculate(double fistOperand, double secondOperand) {
           return fistOperand * secondOperand;
       }
   }
   
   //Divide
   public class DivideOperation implements OperationStrategy {
       @Override
       public double calculate(double fistOperand, double secondOperand) {
           return fistOperand / secondOperand;
       }
   }
   ~~~

4. 위와같이 만든 후 각각의 전략을 매핑시켜줄 클래스를 만듭니다.

   ~~~java
   public class OperationMapping {
       private Map<String, Operation> operations = new HashMap<>();
   
       public void init() {
           operations.put("+", new PlusOperation());
           operations.put("-", new MinusOperation());
           operations.put("*", new MultipleOperation());
           operations.put("/", new DivideOperation());
       }
   
       public Operation findOperation(String operation) {
           return operations.get(operation);
       }
   }
   ~~~

5. 실제로는 다음과 같이 사용할 수 있습니다.

   ~~~java
   //operands는 피연산자를 들고있는 배열, operations는 연산자를 들고있는 배열
   public double operate(double[] operands, String[] operations) {
       for (int i = 0; i < operations.length; i++) {
           Operation operation = mapping.findOperation(operations[i]);
           operands[i + 1] = operation.calculate(operands[i], operands[i + 1]);
       }
   
       return operands[operands.length - 1];
   }
   ~~~

위와 같이 전략패턴을 사용한다면 내부 구현을 감출 수 있으며, 확장성 좋은 설계를 할 수 있습니다.

</br >

좀더 간단하게 만드는 방법으로 enum을 사용할 수 있습니다.

~~~java
public enum OperationType {
    PLUS("+",(x, y) -> x + y),
    MINUS("-",(x, y) -> x - y),
    MULTIPLY("*",(x, y) -> x * y),
    DIVIDE("/",(x, y) -> x / y),

    private String token;
    private CalculatorStrategy operationStrategy;

    CalculatorType(String token, OperationStrategy operationStrategy) {
        this.token = token;
        this.operationStrategy = operationStrategy;
    }

    public static OperationStrategy findByToken(String token){
        return Arrays.stream(OperationType.values())
                        .filter(operationType ->  operationType.isEqualToken(token))
                        .findAny()
                        .operationStrategy;
    }

    private boolean isEqualToken(String token){
        return this.token.equals(token);
    }
}
~~~

