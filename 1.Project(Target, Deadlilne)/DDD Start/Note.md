# Immutable 객체의 장점

참조 투명성, 스레드 안전성

wiki
```

...

Immutable 객체를 사용하면 복제나 비교를 위한 조작을 단순화 할 수 있고, 성능 개선에도 도움을 준다. 하지만 객체가 변경 가능한 Data를 많이 가지고 있는 경우엔 Immutable이 오히려 부적절한 경우가 있다. 이 때문에 많은 프로그래밍 언어에서는 Immutable이나 가변 중 하나를 선택할 수 있도록 하고 있다.

...

Immutable 객체는 멀티 스레드 프로그래밍에서도 유용하다. Data가 Immutable 객체에 저장돼 있다면 복수의 스레드에 의해서 특정한 스레드의 Data가 변경될 우려없이 Data에 접근할 수 있다. 즉, 배타 제어(mutual exclusion)를 할 필요가 없다. 쉽게 말해 Immutable 객체가 가변 객체보다 스레드 세이프(Thread-safe) 하다고 생각하면 된다.
```

# DTO의 get/set

오래전에 사용했던 Framework는 요청 Parameter난 DB Column의 값을 설정할 때 set method를 필요로 했기 때문에 구현 기술을 적용하려면 어쩔수 없이 DTO에 get/set method를 구현 했다.

최근 Framework는 private 필드에 직접 값을 할당할 수 있는 기능 또는 get/set을 자동으로 만들어 주는 기능도 있다.

DTO가 Domain 관련 Logic을 담고 있지는 않기에 get/set method를 제공해도 Domain 객체의 데이터 일관성에 영향을 줄 가능성이 높지는 않다.

# Domain 용어

코드를 작성할 떄 Domain에서 사용하는 용어는 매우 중요. Domain 용어를 사용하여 코드를 작성하기 떄문.

# Presentation Layer, Application Layer 상호 작용

Web Application 개발할 때 많이 사용하는 Spring MVC Framework 가 Presentation Layer 를 위한 기술에 해당한다.


![[KakaoTalk_20231119_201045466.jpg]]


Presentation Layer의 User는 Web Browser를 사용하는 사람일 수 있고, REST API를 호출하는 External System일 수 있다.

Web Application은 HTTP 요청을 Application Layer가 필요로 하는 형식으로 변환해서 Application Layer에 전달. Application Layer의 응답을 HTTP 응답으로 변환해서 전송.