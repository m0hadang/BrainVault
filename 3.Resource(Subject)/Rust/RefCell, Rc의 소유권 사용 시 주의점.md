---
tags:
  - Rust
  - Tool
---
![[rust.png]]

# 쉐도잉 불가

```rust
let base = Rc::new(RefCell::new(GroundStation {  
    ..Default::default()  
}));

...

let mut base = base.borrow_mut();//쉐도잉 불가
base.radio_freq = 10.1;  
println!("freq : {}", base.borrow().radio_freq);
```

다음 코드는 컴파일 에러가 발생한다. 

컴파일 에러가 발생할 경우 다음과 같이 권고 사항이 나온다 

`use std::borrow::Borrow;`

하지만 적절한 대응책은 아니다. borrow, borrow_mut는 std가 아닌 RefCell에 정의되어 있는 메서드를 호출해야 한다.

쉐도잉 대신 명시적으로 이름을 변경 하거나 중첩 스코프를 사용하면 문제를 해결 할 수 있다.

```rust
...

let base = Rc::new(RefCell::new(GroundStation {  
    ..Default::default()  
}));

...

// solution 1
let mut base_2 = base.borrow_mut();
base_2.radio_freq = 10.1;

// solution 2
{  
    let mut base = base.borrow_mut();  
    base.radio_freq = 10.1;  
}

...
```

# 유효하지 않는 소유권

borrow, borrow_mut를 사용할 경우 같은 스코프 안에서 소유권이 이동된다 따라서 다음과 같은 경우 소유권 관련 런타임 에러가 발생할 수 있다.

```rust
let base = Rc::new(RefCell::new(GroundStation {  
    ..Default::default()  
}));

...


let mut base_2 = base.borrow_mut();//소유권 이동. base는 더 이상 소유권 없음
base_2.radio_freq = 10.1;
println!("freq : {}", base.borrow().radio_freq);// 소유권 없는 객체에 접근 하기에 런타임 에러 발생
```


borrow 함수 내부를 보면 다음과 같이 내부에서 expect를 정의하여 런타임 오류 발생 가능성을 시사하고 있다.

```rust
pub fn borrow(&self) -> Ref<'_, T> {  
    self.try_borrow().expect("already mutably borrowed")  
}
```

# borrow, borrow_mut는 사용하지 는 것을 권고

borrow, borrow_mut를 사용하면 Rust의 강점인 컴파일 타임에 메모리 오류, 소유권 검사를 전혀 사용할 수 없다. 예를 들어 Option을 반환할 경우 컴파일 타임에 None에 대한 처리를 하도록 강제하지만 borrow, borrow_mut는 이런 제약이 없다. 마치 unwrap과 같이 개발자가 직접 소유권을 관리 하는 수 밖에 없다.

borrow 역시 unwrap과 같이 사용을 지양하고 사용 하더라도 그 위험성을 자각하고 try_borrow와 같은 대체 함수를 사용하는 것이 좋아 보인다.