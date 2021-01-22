# @PathVariable과 @RequestParam의 차이점

@RequestParam, @PathVariable은 모두 HTTP 요청에서 값을 추출하는데 사용 되지만 미묘한 차이가 있다.

이름에서 알 수 있듯이 @RequestParam은 URL에서 요청 매개 변수를 가져 오는데 사용되며, @PathVariable은 URL에서 값을 추출한다.

### 예제

- HTTP요청: `http://localhost:8080/shop/order/1001/receipts?date=2020-01-22`
- @RequestParam: data 매개 변수를 가져올 수 있다.
- @PathVariable: orderId, 즉 "1001"을 추출 할 수 있다.

~~~java
@GetMapping(value = "/order/{orderId}/receipts")
    public void exam(@PathVariable("orderId") int order,
                     @RequestParam(value = "date", required = false) Date dateOrNull) {
        ...
    }
~~~

`required = false`: query parameter가 null일 수 도 있음을 나타낸다.

</br >

## @RequestParam을 사용하여 Query Parameters 가져오기

예로 주문 및 거래에 대한 세부 정보를 반환하는 URL이 다음과 같다고 가정해보자.

URL: `http://localhost:8080/shop/orders?id=1001`

위 URL에서 query parameters를 이용하기 위해 다음 코드를 사용할 수 있다.

~~~java
@GetMapping("/orders")
public String showOrderDetails(@RequestParam("id") String orderId, Model model){
   model.addAttribute("orderId", orderId);
   return "orderDetails";
}
~~~

</br >

만약 query paramter 변수의 이름과 argument 이름이 동일한 경우 query parameter의 이름을 지정하지 않고 @RequestParam을 사용할 수 있다.

URL: `http://localhost:8080/trades?tradeId=1001`

~~~java
@GetMapping("/trades")
public String showTradeDetails(@RequestParam String tradeId, Model model){
    model.addAttribute("tradeId", tradeId);
    return "tradeDetails";
}
~~~

위 코드는 query parameter의 이름을 따로 지정하지 않았다. 왜냐하면 query paramter 변수 이름과 argument 이름이 동일하기 때문이다.

</br >

## @PathVairable을 사용하여 값 추출하기

@PathVariable은 URL 자체에서 일부 값을 얻는 데 사용된다. 예로 ISBN 번호를 사용하여 책을 검색하는 URL이 다음과 같다고 가정해보자.

URL: `http://localhost:8080/book/1001`

위 URL에서 ISBN 값을 추출하려면 다음 코드를 사용할 수 있다.

~~~java
@GetMapping(value = "/book/{ISBN}", method = RequestMethod.GET)
public String showBookDetails(@PathVariable("ISBN") String id, Model model) {
    model.addAttribute("ISBN", id);
    return "bookDetails";
}
~~~

</br >

@RequestParam과 마찬가지로 variable 이름과 argument 이름이 동일하면 값 속성을 생략 할 수 있다.

~~~java
@GetMapping(value = "/book/{ISBN}", method = RequestMethod.GET)
public String showBookDetails(@PathVariable String ISBN, Model model) {
    model.addAttribute("ISBN", ISBN);
    return "bookDetails";
}
~~~

</br >

## @PathVariable과 RequestParam의 차이점

1. @RequestParam은 query parameter를 추출하는데 사용되며 @PathVariable은 URI에서 바로 데이터를 추출하는데 사용된다.
2. URL을 통해 같은 값을 포함하지만 RESTful Web Service에서는 @PathVariable이 더 적합하다.
   - URL 자체에서 일부 값을 얻기 때문
3. @RequestParam은 required 속성 및 defaultValue 속성을 사용하여 비어있는 경우 기본값을 지정할 수 있다.
   - Spring 4.3.3부터 @PathVariable에서 required 속성을 사용 할 수 있다.
4. @PathVariable은 여러개를 사용할 수 있다.

