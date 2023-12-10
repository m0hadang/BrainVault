# Domain 구성요소

Entity : 고유의 식별자를 갖는 객체.
- 자신의 Life Cycle을 갖는다.
- ex) Member, Product와 같이 Domain 고유한 개념을 표현.
- Domain Model의 데이터를 포함하며 해당 데이터와 관련된 기능을 함께 제공.

Value : 고유의 식별자를 갖지 않는 객체. 개념적으로 하나의 도메인 객체의 속성을 표현할 때 사용.
- ex) 배송지 표현을 위한 Address, 구매 금액 표현을 위한 Money
- Entity의 속성으로 사용될 뿐만 아니라 다른 Value 타입의 속성으로 사용될 수 있다.

Aggregate : Entity와 Value 객체를 개념적으로 하나로 묶은 것.
- ex) Order Aggregate = Order Entity + OrderLine Value

Repository : Domain Model의 영속성 처리.
- ex) DBMS 테이블에서 Entity 객체를 로딩하거나 저장.

Domain Service : 특정 Entity에 속하지 않은 Domain 로직을 제공.
- ex) 할인 금액 계산 : 상품,쿠폰, 회원 등급, 구매 금액 등 다양한 조건을 이용해서 구현하게 되는데, 이렇게 도메인 로직이 여러 Entity와 Value를 필요홀 할 경우 Domain Service에서 로직을 구현


![[Domain Model Component.png]]


# Domain Model Entity vs RDBMS Entity

도메인 모델의 Entity와 DB의 Entity는 같은 것이 아니다. 

두 모델의 가장 큰 차이점은 Domain 모델의 Entity는 데이터와 함께 도메인 기능을 함께 제공한다.

ex) 주문을 표현하는 Entity는 주문과 관련된 데이터뿐만 아니라 배송지 주소 변경을 위한 기능을 함께 제공.

```java
class Order {
	private OrderNo number;
	private Orderer orderer;
	private ShippingInfo shippingInfo;
	public void changeShippingInfo(ShippingINfo newShippingInfo) ...
}
public class Orderer {
    private String name;
    private String email;
}
```

Domain Model Entity
- 단순히 데이터를 담고 있는 데이터 구조라기보다는 데이터와 함께 기능을 제공하는 객체.
- 도메인 모델의 엔티티는 두 개 이상의 데이터가 개념적으로 하나인 경우 Value 타입을 이용해서 표현할 수 있다(ex: Orderer)

RDBMS Entity
- 관계형 DB는 Value 타입을 제대로 표현학 힘들다. 이런 데이터를 표현하기 위해서는 별도의 테이블을 생성하여 관계를 맺어야 한다.

# Value의 불변
- Value는 불변으로 구현하는 것을 권고하며 이는 Entity의 Value 타입 데이터를 변경할 때 객체 자체를 완전히 교체한다는 것을 의미한다
```java
class Order {
	public void changeShippingInfo(ShippingINfo newShippingInfo) {
	    setShippingInfo(newShippingInfo);
	}
}
```

# 도메인 모델에서 전체 구조를 이해하는데 도움이 되는 Aggregate

![[KakaoTalk_20231126_115725997.jpg]]


Domain이 커질수록 개발할 Domain Model도 커지면서 많은 Entity와 Value가 출현한다.

Domain Model이 복잡해지면 개발자가 전체 구조가 아닌 한 개 Entity와 Value에만 집중하게 되는 경우가 발생한다. 

이때 상위 수준에서 모델을 관리하기보다 개별 요소에만 초점을 맞추가 보면 큰 수준에서 모델을 이해하지 못해 큰 틀에서 모델을 관리할 수 없는 상황에 빠 질 수 있다.

Aggregate는 관련 객체를 하나로 묶은 군집이다

![[KakaoTalk_20231126_120042403.jpg]]

ex) 주문이라는 도메인 개념은 "주문", "배송지 정보", "주문자", "주문 목록", "총 결제 금액"의 하위 모델로 구성. 이때 하위 개념을 표현한 모델을 하나로 묶어서 "주문"이라는 생위 개념으로 표현할 수 있다.

Aggregate를 사용하면 개별 객체가 아닌 관련 객체를 묶어서 객체 군집 단위로 모델을 바라볼 수 있게 된다.

Aggregate는 군집에 속한 객체들을 관리하는 루트 Entity를 갖는다. 루트 Entity는 애그리거트에 속해 있는 Entity와 Value 객체를 이용해서 Aggregate가 구현해야 할 기능을 제공한다.

Aggregate를 사용하는 코드는 Aggregate 루트가 제공하는 기능을 실행하고 Aggregate를 통해서 간접적으로 Aggregate 내의 다른 Entity나 Value에 접근하게 된다. 이는 Aggregate의 내부 구현을 숨겨서 Aggregate 단위로 구현을 캡슐화 할 수 있도록 돕니다.


ex) Aggregate Root인 Order는 주문 도메인 로직에 맞게 애그리거트의 상태를 관리한다. 주문 애그리거트는 Order를 통하지 않고 ShippingInfo를 변경할 수 있는 방법을 제공하지 않는다.

Aggregate를 구현할 때는 고려할 것이 많아
- 어떻게 구성했느냐에 따라 구현이 복잡해짐
- 어떻게 구성했느냐에 따라 트랜잭션 범위 달라짐
- 선택한 구현 기술에 따라 Aggregate 구현에 제약 발생

# 객체의 영속성을 관리하는 Repository

Repository 역시 Domain Model 이다.

도메인 객체를 지속적으로 사용하려면 RDBMS, NoSQL, 로컬 파일과 같은 물리적인 저장소에 도메인 객체를 보관해야 한다. 이를 위한 Domain Model이 리포지터리이다.

리포지터리는 Aggregate 단위로 도메인 객체를 저장하고 조회하는 기능을 정의한다.

리포지터리는 대상을 찾고 저장하는 단위가 애그리거트 단위이다.

ex) OrderRepository 메서드를 보면 대상을 찾고 저장하는 단위가 애그리거트 루트인 Order이다. Order는 애그리거트에 속한 모든 객체를 포함하고 있으므로 결과적으로 애그리거트 단위로 저장하고 조회한다.

```java
public interface OrderRepository {
	public Order findByNumber(OrderNumber number);
	public void save(Order order);
	public void delete(Order order);
}
```


도메인 모델을 사용하는 코드는 리포지터를 통해서 도메인 객체를 구한 뒤에 도메인 객체의 기능을 실행하게 된다.

![[KakaoTalk_20231126_122018771.jpg]]

도메인 모델 관점에서 Orderrepository는 도메인 객체를 영속화하는 데 필요한 기능을 추상화한 것으로 고수준 모듈에 속한다. 기반 기술을 이용해서 OrderRepository를 구현한 클래스는 저수준 모듈로 인프라 영역에 속한다.

![[제목 없는 다이어그램.drawio (5).png]]

응용 서비스는 의존 주입과 같은 방식을 사용해서 실제 리포지터리 구현 객체에 접근한다.

응용 서비스와 리포지터리는 밀접한 연관이 있다
- 응용 서비스는 필요한 도메인 객체를 구하거나 저장할 때 리포지터리를 사용한다.
- "응용 서비스는 트랜잭션을 관리하는데, 트랜잭션 처리는 리포지터리 구현 기술에 영향을 받는다."

리포지터리의 사용 주체가 응용 서비스이기 떄문에 리포지터리는 으용 서비스가 필요로 하는 메서드를 제공한다. 가진 기본이 되는 메서드는 다음의 두 메서드이다.
- 애그리거트를 저장하는 메서드
- 애그리거트를 루트 식별자로 조회하는 메서드
```java
public interface SomeRepository {
	void save(Some some);
	Some findById(SomeId id);
}
```


# 웹 요청 처리 Architecture


![[제목 없는 다이어그램.drawio (3).png]]

ex) 예매/취소 기능을 제공하는 응용 서비스
- 도메인의 상태를 변경하므로 변경 상태가 물리 저장소에 올바르게 반영되도록 트랜잭션을 관리해야 한다. 스프링 프레임워크를 사용하면 다음과 같이 스프링이 제공하는 트랜잭션 관리 기능을 이용해서 트랜잭션을 처리할 수 있다.

![[KakaoTalk_20231126_124433871.jpg]]


# 인프라스트럭처에 대한 의존

일반적으로 도메인 영역과 응용 영역에서 인프라의 기능을 직접 사용하는 것 보다 DIP를 적용하여 두영역에서 정의한 인터페이스를 인프라 영역에서 구현하는 것이 시스템을 더 유연하고 테스트하기 쉽게 만들어 준다.

"하지만 무조건 인프라에 대한 의존을 없애는 것이 좋은 것은 아니다"
- 스프링을 사용할 경우 @Transactional을 사용하는 것이 편리.
- 영속성 처리를 위한 JPA를 사용할 경우 @Entity, @Table과 같은 JPA 전용 어노테이션을 도메인 모델 클래스에 사용하는 것이 XML 매핑 설정을 이용하는 것보다 편리
- 이런 편리한 기능은 DIP가 아닌 직접적으로 인프라에 의존한다.

"구현의 편리함은 DIP가 주는 다른 장점만큼 중요하기 떄문에 DIP의 장점을 해치지 않는 범위에서 응용 영역과 도메인 영역에서 구현 기술에 대한 의존을 가져가는 것이 현명하다.

응용 영역과 도메인 영역이 인프라에 댛나 의존을 완전히 갖지 않도록 시도하는 것은 자칫 구현을 더 복잡하고 어렵게 만들 수 있다.(@Transactional 의존을 없다고 생각 해보아라)"

# 모듈 구성

영역별로 패키지 구성.

![[KakaoTalk_20231126_125426371.jpg]]


도메인 별로 패키지 구성.

![[KakaoTalk_20231126_125426062.jpg]]


도메인 안에서 하위 도메인 패키지 구성.

![[KakaoTalk_20231126_125425664.jpg]]

