# JPA delete가 안되는 현상

JpaRepository에서 delete() 메서드를 호출하여 삭제하려고 하는데 삭제가 안되는 현상이 발생했다..!

코드는 에러 없이 잘 돌아갔는데 자세히 보니 delete 쿼리가 실행되지 않았다.

## 소스 코드

### Entity

```java
@Entity
@Getter
@NoArgsConstructor(access = PROTECTED)
public class Product {
    ...
    @OneToMany(mappedBy = "product", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<InterestProduct> interestProductList = new ArrayList<>();
    ...
}
```

~~~java
@Entity
@Getter
@NoArgsConstructor(access = PROTECTED)
public class InterestProduct {
    ...
    @ManyToOne(fetch = LAZY)
    @JoinColumn(name = "product_id")
    private Product product;
    ...
}
~~~

</br >

### Delete Logic

```java
...
@Transactional
public Long cancel(Long productId, Long interestedUserId) {
    final Product product = productFindService.findProduct(productId);
    final User interestedUser = userFindService.findUser(interestedUserId);
    final InterestProduct interestProduct = interestProductRepository.findByProductAndInterestedUser(product, interestedUser)
            .orElseThrow(() -> new IllegalArgumentException("not found interestProduct."));

    interestProductRepository.delete(interestProduct);
    return interestProduct.getId();
}
...
```

</br >

## 코드 설명

회원(User)이 관심 상품으로 등록한 상품(Product)이 맞다면 관심 상품(InterestProduct)을 찾고, 해당 관심 상품을 삭제하는 로직이다.

이 과정에서 JpaRepository의 delete() 메서드를 호출하였고, 코드는 에러 없이 잘 돌아갔다. 그런데 어째서인지 delete 쿼리가 실행되지 않았고, 실제 DB에도 삭제되지 않고 온전히 남아있었다.

</br >

## 해결 방법

상품(Product)를 **같은 Transaction**에서 검색한 후 관심 상품(InterestProduct)을 삭제할 경우 Product가 갖고 있는 InterestProduct도 같이 지워줘야 했다.

~~~java
...
@Transactional
public Long cancel(Long productId, Long interestedUserId) {
    final Product product = productFindService.findProduct(productId);
    final User interestedUser = userFindService.findUser(interestedUserId);
    final InterestProduct interestProduct = interestProductRepository.findByProductAndInterestedUser(product, interestedUser)
            .orElseThrow(() -> new IllegalArgumentException("not found interestProduct."));

    product.deleteInterestProduct(interestProduct); // Product가 갖고 있는 InterestProduct 삭제
    interestProductRepository.delete(interestProduct);
    return interestProduct.getId();
}
...
~~~

![img](https://blog.kakaocdn.net/dn/BD9S2/btq5t3w62UA/fK580Z5PhP9xUesx6TnPl1/img.png)

</br >

같은 Transaction이 아니면(**해당 Transaction에 Product를 포함하지 않으면**) InterestProduct가 잘 삭제되는 것도 확인할 수 있었다.

~~~java
@Transactional
public Long cancel(Long interestProductId, Long interestedUserId) {
    final InterestProduct interestProduct = findInterestProductById(interestProductId);
    final User user = userFindService.findUser(interestedUserId);
    if (interestProduct.isNotBelongToUser(user)) {
        throw new IllegalArgumentException("user is not registered a interestProduct");
    }
    
    interestProductRepository.delete(interestProduct);
    return interestProduct.getId();
}
~~~

![image-20210521161222212](/Users/bang/Library/Application Support/typora-user-images/image-20210521161222212.png)

**한 transaction 안에서 서로 연관관계가 있는 두 Entity를 변경할 때 동기화를 꼭 해주자!**