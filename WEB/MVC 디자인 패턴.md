## MVC 디자인 패턴

### MVC란

- Model View Controller의 약자로 어플리케이션을 세가지의 역할로 구분한 개발 방법론이다.
- 사용자가 Controller를 조작하면 COntroller는 Model을 통해 데이터르르 가져오고 그 정보를 바탕으로 시각적인 표현을 담당하는 View를 제어해서 사용자에게 전달하게 된다.



![img](https://mblogthumb-phinf.pstatic.net/MjAxNzAzMjVfMjUw/MDAxNDkwNDM4NzI4MTIy.4ZtITJJKJW_Nj1gKST0BhKMAzqmMaYIj9PobYJMFD4Ig.xTHT-0qyRKXsA4nZ2xKPNeCxeU2-tLIc-4oyrWq5WBgg.PNG.jhc9639/mvc_role_diagram.png?type=w800)

### Model

- 데이터를 가지고 있는 객체를 뜻한다.

- 비지니스 로직에서의 알고리즘, 데이터 등의 기능을 처리한다.
- 일반적으로 데이터베이스 테이블에 대응된다.



### Controller

- 사용자가 접근한 URL에 따라서 사용자의 요청을 파악한 후, 그 요청에 맞는 데이터를 Model에 의뢰한다.
  데이터를 View에 반영해서 사용자에게 알려준다
- 모델 객체로의 데이터 흐름을 제어하고 데이터가 update 되었을 때 뷰를 갱신한다.
  뷰와 모델의 역할을 분리한다.



### View

- 모델에 포함된 데이터의 시각화를 담당한다.

- 클라이언트 측 기술인 html, css, js들을 모아둔 컨테이너다.
- 사용자가 보게 될 결과 화면을 출력한다.