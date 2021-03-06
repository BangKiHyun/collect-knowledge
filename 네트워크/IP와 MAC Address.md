## IP Address

#### IP Address란

- 네트워크 상 컴퓨터(노드)간 통신을 하기 위해 부여된 **각 노드의 위치주소**를 말한다.
- IP는 **네트워크 주소+호스트 주소**로 구성되면 하나의 네트워크 주소 안에 호스트 주소는 각자 달라야한다.



#### IP Address 구성

- IPv4 체계를 기준으로 12개의 숫자로 이루어져 있다. 점(.)으로 구분되어진 4개의 그룹으로 구성되며, 각 그룹은 0~255의 범위를 갖는다.

  #### 0.0.0.0 ~ 255.255.255.255

- IP 주소를 따라다니는 서브넷 마스크(Subnet Mask)라는 것이 잇는데, 이것은 IP 주소에서 네트워크 주소와 호스트 주소를 나눠주는 역할을 한다.
- 호스트(개인)들 간의 네트워크 통신은 **같은 네트워크 주소/네트워크 대역(같은 국가)** 내에서만 이루어 진다.
  다른 네트워크 대역의 호스트들과 연결하는 방법은 라우팅(Routing)에 관련이 있다
- 똑같은 IP 주소라 하더라도, 서브넷 마스크가 다르면, IP 주소가 의미하는 바가 달라진다.





## MAC Address

#### MAC Address란

- MAC 주소는 IP주소와 마찬가지로 네트워크 통신에서 통신기기의 식별번호를 나타내는 것이다.
- IP주소와의 차이점은 IP주소는 임시적으로 다른 주체에 의해 할당되는 것이지만, MAC주소는 통신기기의 하드웨어 자체에 부여된 고유한 식별번호를 나타낸다.
  즉, 제품의 시리얼 넘버라고 생각할 수 있다.
- 세상에 단 하나밖에 없는 유니크한 값을 가지며, 변경될 수 없다.
- MAC주소는 외부에서 내부의 사설 IP 통신 요청을 할 때 중요한 역할을 한다. 사설 IP는 외부에서 볼 수 없기 때문에 외부에서는 어떤 사설 IP 최종 목적지인지 알 수 없다.
  이 떄 최종 목적지의 MAC주소를 알고 있다면 IP주소에 구애받지 않고 목적지까지 도달할 수 있게 된다.





### 추가

#### ARP(Address Resolution Protocol)

- IP주소를 이용해 MAC주소를 알아내는 프로토콜이다
  즉, IP주소만 알고 있을 때 그 IP에 해당하는 MAC주소를 결정하는 프로토콜
- 논리 주소를 물리 주소로 변환
- 주소 결정 프로토콜



#### RARP(Reverse Address Resolution Protocol)

- MAC주소를 이용해 IP주소를 알아내는 프로토콜이다
- 물리 주소를 논리 주소로 변환