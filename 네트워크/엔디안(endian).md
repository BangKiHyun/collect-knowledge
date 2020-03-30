# endian

### endian이란

컴퓨터의 메모리와 같은 2차원의 공간에 여러 개의 연속된 대상을 배열하는 방법을 뜻함
바이트를 배열하는 방법을 특히 바이트 순서라 한다



![img](https://images.squarespace-cdn.com/content/v1/549dcda5e4b0a47d0ae1db1e/1490746414666-EM74IA60AFM16OEH9G22/ke17ZwdGBToddI8pDm48kOMlUb6YZjvz-j7uj5wTIAtZw-zPPgdn4jUwVcJE1ZvWQUxwkmyExglNqGp0IvTJZamWLI2zvYWH8K3-s_4yszcp2ryTI0HqTOaaUohrI8PICROjhJFkM8GI5jSypQ9qrB6ZUKEpH8g8X8GW3p0wQZI/image-asset.png)

### Big endian

- 최상위 바이트(MSB-Most Significant Byte)부터 차례로 저장하는 방식
  즉, 사람이 글을 읽는 방법과 같이 큰 단위의 바이트가 앞에 오는 방법

- 0x123456 이라는 16진수 데이터를 메모리에 저장한다고 가정하면
  16진수 한 자리수가 4bit를 차지하므로 최소 단위인 1Byte씩 나눠보면 두 숫자가 할당(1byte = 8bit)

  ### 12 | 34 | 56 



### Little endian

- 최하의 바이트(LSB-Least Significant Byte)부터 차례로 저장하는 방식
  즉, 작은 단위의 바이트가 앞에 오는 방법

- 0x123456 이라는 16진수 데이터를 메모리에 저장한다고 가정하면
  65 | 43 | 21로 저장되지 않는 이유는 가장 최소 단위가 1byte(8bit)이기 때문에 1byte씩 읽어옴!

  ### 56 | 34 | 12



### 장단점

- 빅 엔디안은 사람이 숫자를 읽고 쓰는 방법과 같기 때문에 디버깅 과정에서 메모리의 값을 보기 편하기 때문에 디버그를 편하게 해줌
  또한, 두 개의 숫자를 비교할 때 빠른 연산이 가능함
- 리틀 엔디안은 첫 바이트 하나만 보면 짝수인지 홀수인지 금방 알 수 있음
  그리고 연산과정에서 자릿수 증가가 생겼을 때 빅 엔디안 방식보다 빠름
- 리틀 엔디안 방식은 포인터의 값 참조 시 유리하다
  예를 들어, char *x라는 포인터 변수가 있고, 이 메모리의 주소는 0x12345678이라고 가정했을 때
  y = *x와 같이 포인터 변수가 가리키는 값을 참조하고자 할 경우, 시작 주소에서 1byte만 가져오면 자연스럽게 가장 낮은 바이트인 0x78가 참조되게 된다