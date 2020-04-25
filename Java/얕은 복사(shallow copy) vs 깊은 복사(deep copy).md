## 얕은 복사(shallow copy) vs 깊은 복사(deep copy)

얕은 복사(call by reference)

- 객체의 참조값(주소값)을 복사하는 것

깊은 복사(call by value)

- 객체의 실체값을 복사하는 것



### ArrayList를 사용한 얕은 복사와 깊은 복사

```
public class CopyTest{
    ArrayList<String> origin = new ArrayList<String>();
    ArrayList<String> copy = new ArrayList<String<();
    
    public void init() {
        origin.add("apple");
        origin.add("banana");
        origin.add("grape");
    }

//얕은 복사(shallow copy)버전
    public void shallowCopy{
        copy = origin;
        copy.add("strawberry");
        
        for(String fruit : origin){
        	System.out.println("origin : " + fruit);            
        }
        
        for(String fruit : copy){
            System.out.println("copy : " + fruit)
        }
    }
    
//깊은 복사(deep copy)버전
    public void shallowCopy{
        copy.addAll(origin); //또는 copy = origin.clone();
        copy.add("strawberry");
        
        for(String fruit : origin){
        	System.out.println("origin : " + fruit);            
        }
        
        for(String fruit : copy){
            System.out.println("copy : " + fruit)
        }
    }
}
```

### 결과

- 얕은 복사

  origin : apple			copy : apple
  origin : banana		copy : banana
  origin : grape		copy : grape
  origin : strawberry	copy : strawberry

얕은 복사의 결과 copy 에만 추가한 strawberry 가 origin 에도 추가되어 있다.
즉, shallow copy 는 원본과 복사본 둘 중 한쪽의 수정이 **양쪽 모두에게 영향**을 미치게 된다.



- 깊은 복사

  origin : apple			copy : apple
  origin : banana		copy : banana
  origin : grape		copy : grape
  					copy : strawberry

깊은 복사의 결과 copy 에만 추가한 strawberry 가 origin 에는 추가되지 않았다.
즉, deep copy 는 원본과 복사본 **서로 에게 영향을 미치지 않는**다.



### ArrayList의 Item 으로 객체(Object)가 선언되어 있을 때

```
class Fruit{
    private String name;
    private String color;
    
    public Fruit(String name, String color){
        this.name = name;
        this.color = color;
    }
    
    public String getName(){
        return name;
    }
}

public class CopyTest{
    ArrayList<Fruit> origin = new ArrayList<Fruit>();
    ArrayList<Fruit> copy = new ArrayList<Fruit<();
    
    public void init() {
        origin.add(new Fruit("apple", "빨강"));
        origin.add("banana", "노랑");
        origin.add("grape", "보라");
    }

//얕은 복사(shallow copy)버전
    public void shallowCopy{
        copy = origin;
        Fruit fruit = new Fruit("strawberry", "빨강");
        copy.add(fruit);
        
        for(Fruit fruit : origin){
        	System.out.println("origin : " + fruit.name);            
        }
        
        for(Fruit fruit : copy){
            System.out.println("copy : " + fruit.name)
        }
    }
    
//깊은 복사(deep copy)버전
    public void shallowCopy{
        copy.addAll(origin); //또는 copy = origin.clone();
        Fruit fruit = new Fruit("strawberry", "빨강");
        copy.add(fruit);
        
        for(String fruit : origin){
        	System.out.println("origin : " + fruit);            
        }
        
        for(String fruit : copy){
            System.out.println("copy : " + fruit)
        }
    }
}
```

### 결과

- 얕은 복사

  origin : apple			copy : apple
  origin : banana		copy : banana
  origin : grape		copy : grape
  origin : strawberry	copy : strawberry

- 깊은 복사

  origin : apple			copy : apple
  origin : banana		copy : banana
  origin : grape		copy : grape
  					copy : strawberry

그렇다면 Fruit 객체애 대한 깊은 복사가 이러우 진 것일까?
위 코드에서 origin 의 apple 객체의 name 을 "strawberry"으로 바꾼다면 어떻게 될지 테스트를 해보자

```
...
copy.addAll(origin); //또는 copy = origin.clone();
Fruit fruit = copy.get(0);
fruit.setName("strawberry");
```

### 결과

​	origin : strawberry
​	origin : banana
​	origin : grape

분명히 깊은 복사를 하였지만 origin 과 copy 의 apple 객체 name 이 모두 "strawberry" 로 변경됐다.
즉, 두 ArrayList 의 포함된 Fruit 은 같은 객체라는 것을 의미한다.

해결 방법으로는 해당 객체(Fruit)에 대한 깊은 복사를 수행하면 된다.

```
...
for(Fruit fruit : origin){
    copy.add(new Fruit(fruit));
}
```

추가적으로 Cloneable Interface는 따로 공부!