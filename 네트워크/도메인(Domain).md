## 도메인(Domain)

### 도메인이란

- ip는 사람이 이해하고 기억하기 어렵기 때문에 이를 위해서 각 **ip에 이름을 부여**할 수 있게 했는데, 이것을 도메인이라고 한다.
  즉, 인터넷에 연결된 컴퓨터를 사람이 **쉽게 기억하고 입력**할 수 있도록 문자(영문 한글)로 만든 인터넷 주소다.

  ex) naver.com -> 220.95.233.172



### 도메인 구성요소

- 도메인은 루트(root)라 불리는 도메인 이하에 **역트리구조**로 구성이 되어있다.
- 루트 도메인 아래의 단계를 1단계 도메인 또는 최상위 도메인(TLD, Top Level Domain)이라고 부르며,
  그 다음 단계를 2단계(Second Level Domain)이라고 부른다.

![도메인이란?](https://t1.daumcdn.net/cfile/tistory/232A484D5905623334)

- 일반 최상위 도메인
  com(회사), net(네트워크 관련기관), org(비영리기관), biz(사업) 등이 있다.

- 국가 최상위 도메인 
  인터넷 상으로 국가를 나타내는 도메인으로
  kr(대학민국), jp(일본), cn(중국), us(미국) 등 영문으로 구성된 영문 국가도메인이 있다.

  ex) daum.co.kr

   - daum : 컴퓨터 이름
   - co : 국가 형태의 최상위 도메인
   - kr : 대한민국의 NIC에서 관리하는 도메인



### URL이란

- Unifrom Resource Locaators의 약자로
  사용자가 원하는 정보를 찾기 위해서 해당 정보 자원의 위치과 종류를 파악해야 할 때 사용하는 일련의 규칙
- 이 안에는 네트워크 상에 퍼져있는 특정 정보 자원의 종류와 위치가 기록되어 있다
- **URL = 도메인(Domain) + 경로** 로 이루어져 있다.
  ex) http://www.asdf.com/user/login.html가 있을 때
  - 도메인 : www.asdf.com
  - user : 자원의 위치
  - login.html : 자원
  - URL : http://www.asdf.com/user/login.html