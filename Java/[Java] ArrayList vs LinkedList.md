# [Java] ArrayList vs LinkedList

상황에 따라 ArrayList와 LinkedList를 적절히 활용한다면 더 좋은 효율성을 보여줄 수 있을텐데요, 어떨 때 어떤 자료구조를 사용하면 좋을지 한번 알아보겠습니다.

</br >

## 구조

ArrayList와 LinkedList를 그림으로 보면 다음과 같습니다.

- ArrayList는 데이터들이 순서대로 쭉 늘어선 배열의 형식을 취하고 있습니다.
- LinkedList는 순서대로 늘어선 것이 아니라 자료의 주소 값으로 서로 연결되어 있는 구조를 하고 있습니다.

<img width="720" alt="image" src="https://user-images.githubusercontent.com/43977617/198827437-dfa7480b-7975-469e-97d7-c3ac564df4f8.png">

</br >

## ArrayList

- **동적 배열**을 사용하여 요소를 저장합니다.
- 데이터를 저장하고 접근하는데 좋습니다.
  - 특정 자료형들이 메모리 공간 상에서 연속적으로 이루어져 있습니다.
  - 인덱스로 해당 원소에 접근할 수 있으며, 인덱스를 알고 있다면 O(1)의 시간 복잡도로 원소에 접근이 가능합니다.
- 삭제 또는 삽입 과정에서는 내부적을 배열을 사용하기 때문에 느립니다. O(n)
  - 배열에서 요소가 삭제 또는 추가되면 다른 모든 요소는 메모리에서 이동되기 때문입니다.
- 메모리에는 ArrayList가 선언되자 마자 Compile time에 할당됩니다.

</br >

### ArrayList 구현부

**선언부**

ArrayList 선언부를 보시면 eleentData라는 배열이 존재합니다.

해당 배열에 실제 값들을 넣게 되어있습니다.

<img width="569" alt="image" src="https://user-images.githubusercontent.com/43977617/198827507-bd227733-f878-4896-8a49-28af8e07db96.png">

**데이터 추가(add)**

데이터를 추가하는 부분을 보면 if문으로 분기처리가 되어있습니다.

현재 size와 배열의 크기가 같다면 배열을 증가시킵니다. 즉, 새로운 배열을 만들어서 옮기는 식입니다.

데이터 추가시 매번 새로운 배열을 만들어서 옮기면 오래걸릴꺼 같습니다.

<img width="698" alt="image" src="https://user-images.githubusercontent.com/43977617/198827654-4027ca44-99dd-4b59-9953-193cafcc232f.png">

**데이터 접근(get)**

데이터를 접근하는데 있어서는 배열에서 값을 바로 가져옵니다.

<img width="565" alt="image" src="https://user-images.githubusercontent.com/43977617/198827554-c1a6413a-4cfb-4be4-950b-09f27f842f8e.png">

</br >

## LinkedList

- **두 개의 노드**를 사용하여 요소를 저장합니다.
- 데이터를 삭제 또는 추가하는데 좋습니다.
  - 두 개의 노드를 사용하기 때문에 **메모리상에서 비트 이동이 필요하지 않습니다.**
- 데이터 검색 시 처음 노드부터 순회해야 합니다 .
  - 이유는 논리적 저장 순서와 물리적 저장 순서가 다르기 때문입니다. O(n)
- 메모리 공간 상에서 각 노드들이 연속적으로 이루어져 있지 않고 흩어져 있으며, 각각의 노드가 자신의 다음 노드의 위치를 알고 있는 형태입니다.
- 중간 삽입 없이 맨 앞과 맨 뒤에만 삽입한다면 O(1)의 시간 복잡도를 갖습니다.
- ArrayList의 경우, 데이터를 삽입하여 모든 공간이 꽉 차게 되면 새로운 메모리 공간을 할당받아 옮겨야 하지만, LinkedList를 그럴 필요가 없습니다.
  - 추가할 때마다 동적으로 메모리 공간을 할당받기 떄문입니다.
- 메모리는 새로운 Node가 추가될 때 runtime에 할당됩니다.

</br >

### LinkedList 구현부

**선언부**

자세히 보시면 두 개의 Node를 선언한 것을 볼 수 있습니다.

아 두 개의 노드를 사용해서 값을 저장합니다.

<img width="694" alt="image" src="https://user-images.githubusercontent.com/43977617/198827615-e458f882-f90e-4f36-93f5-54a725d84d48.png">

**데이터 추가(add)**

데이터 추가 구현부 중 하나 입니다.

보시면 Node하나를 만들어서 값을 추가하는 모습을 볼 수 있습니다.

<img width="698" alt="image" src="https://user-images.githubusercontent.com/43977617/198827630-debbc71b-2d90-46ce-b817-7ffbf7901c37.png">

**데이터 접근(get)**

LinkedList는 데이터를 접근하기 위해 두 개의 노드 중 하나에서 값을 하나하나씩 찾게 되어있습니다.

<img width="698" alt="image" src="https://user-images.githubusercontent.com/43977617/198827671-0e514153-bda7-434b-8b31-9e5b890286d6.png">

</br >

### **결론**

- 데이터 추가 및 삭제가 빈번하게 일어난다면 LinkedList를 사용하는 것이 좋습니다.
- 데이터에 접근하는 것이 빈번하게 일어난다면 ArrayList를 사용하는 것이 좋습니다.
- LinkedList는 메모리 오버헤드가 ArrayList에 비해 더 많습니다.
  - LinkedList는 이전 노드와 다음 노드의 주소를 저장하는 데 필요한 두 개의 추가 링크로 인해 추가 메모리를 소비하기 때문입니다.