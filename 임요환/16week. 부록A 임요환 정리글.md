# 간략한 내용 정리

# 부록A 계약에 의한 설계
- 인터페이스만으로 객체의 행동에 관한 다양한 관점을 전달하기 어려우므로 계약에 의한 설계를 사용하여 협력에 필요한 다양한 제약과 부수효과를 명시적으로 정의하고 문서화할 수 있음
- 주석과 다르게 시감의 흐름에 뒤처질 걱정을 할 필요가 없음

## A-1 협력과 계약
### 부수효과를 명시적으로
- 객체지향의 핵심은 협력 안에서 객체들이 수행하는 행동이며 프로그래밍 언어는 객체가 수신할 메시지를 정의할 수는 있지만 명확한 의사소통 방식은 정의할 수 없음
- 예를 들어 무언가가 isTrue 메서드를 호출하고 true일 때 go라는 메서드를 실행할 수 있다면 이를 인터페이스만으로 순서와 관련된 제약을 설명하기는 어려움
- 라이브러리나 문서화를 사용하여 검증할 수 있음

### 계약
- 각 계약 당사자는 계약으로부터 이익(benefit)을 기대하고 이익을 얻기 위해 의무(obligation)을 이행함
- 각 계약 당사자의 이익과 의무는 계약서에 문서화됨
- 한쪽의 의무가 반대쪽의 권리가 됨
- 계약이라는 아이디어를 협력하는 방식에도 적용할 수 있지 않을까?

## A-2 계약에 의한 설계
- 계약은 협력에 참여하는 두 객체 사이의 의무와 이익을 문서화한 것
- 계약에 의한 설계 개념은 인터페이스에 대해 프로그래밍하라는 원칙을 확장한 것
- 메서드의 이름과 매개변수의 이름을 통해 클라이언트에게 어떤 것을 제공하려는지 충분히 설명할 수 있음
- 사전조건(precondition)
    - 메서드가 호출되기 위해 만족돼야 하는 조건
    - 메서드의 요구사항을 명시함
    - 사전조건이 만족되지 않을 경우 메서드가 실행돼서는 안됨
    - 사전조건을 만족시키는 것은 메서드를 실행하는 클라이언트의 의무임
- 사후조건(postcondition)
    - 메서드가 실행된 후에 클라이언트에게 보장해야 하는 조건
    - 클라이언트가 사전조건을 만족시켰다면 메서드는 사후조건에 명시된 조건을 만족시켜야 함
    - 클라이언트가 사전조건을 만족시켰는데도 사후조건을 만족시키지 못한 경우에는 클라이언트에게 예외를 던져야 함
    - 사후조건을 만족시키는 것은 서버의 의무임
- 불변식(invariant)
    - 항상 참이라고 보장되는 서버의 조건
    - 메서드가 실행되는 도중에는 불변식을 만족시키지 못할 수 있찌만 메서드를 실행하기 전이나 종료된 후에 불변식은 항상 참이어야 함

### 사전조건
- 사전조건을 만족시키지 못하면 클라이언트에게 빠르게 알려야 함
- 계약에 의한 설계를 사용하면 계약만을 위해 준비된 전용 표기법을 사용해 계약을 명확하게 표현할 수 있음

### 사후조건
- 인스턴스 변수의 상태가 올바른지를 서술하기 위해
- 메서드에 전달된 파라미터의 값이 올바르게 변경됐는지를 서술하기 위해
- 반환값이 올바른지를 서술하기 위해

### 불변식
- 클래스의 모든 인스턴스가 생성된 후에 만족돼야 함 -> 클래스에 정의된 모든 생성자는 불변식을 준수해야 함
- 클라이언트에 의해 호출 가능한 모든 메서드에 의해 준수돼야 함
- 메서드가 실행되는 중에는 객체의 상태가 불안정한 상태로 빠질 수 있기 때문에 불변식을 만족시킬 필요는 없지만 메서드 실행 전과 종료 후에는 항상 불변식을 만족하는 상태가 유지돼야 함

## A-3 계약에 의한 설계와 서브타이핑
- 계약에 의한 설계의 핵심은 클라이언트와 서버 사이의 견고한 협력을 위해 준수해야 하는 규약을 정의한 것
- 서브타입이 리스코프 치환 원칙을 만족시키기 위해서는 클라이언트와 슈퍼타입 간에 체결된 계약을 준수해야 함
- 리스코프 치환 원칙의 규칙 두가지 종류
    - 협력에 참여하는 객체에 대한 기대를 표현하는 계약 규칙
    - 교체 가능한 타입과 관련된 가변성 규칙
- 계약 규칙(contract rules)
    - 서브타입에 더 강력한 사전조건을 정의할 수 없음
    - 서브타입에 더 완화된 사후조건을 정의할 수 없음
    - 슈퍼타입의 불변식은 서브타입에서도 반드시 유지돼야 함
- 가변성 규칙(variance rules)
    - 서브타입의 메서드 파라미터는 빈공변성을 가져야 함
    - 서브타입의 리턴 타입은 공변성을 가져야 함
    - 서브타입의 슈퍼타입이 발생시키는 예외와 다른 타입의 예외를 발생시켜서는 안됨
- 계약에 의한 설계는 협력을 올바르게 설계하기 위해 고려해야 하는 설계 원칙과 설계 방법이지 특정한 구현 메커니즘이 아님

### 계약 규칙
#### 서브타입에 더 강력한 사전조건을 정의할 수 없다
- 서브타입이 슈퍼타입에 정의된 사전조건을 강화하면 기존에 체결된 계약을 위반하게 됨
- 사전조건 강화는 리스코프 치환 원칙 위반임
- 사전조건 완화는 리스코프 치환 원칙을 위반하지 않음

#### 서브타입에 더 완화된 사후조건을 정의할 수 없다
- 사후조건을 완화한다는 것은 서버가 클라이언트에게 제공하겠다고 보장한 계약을 충족시켜주지 못한다는 것을 의미함
- 사후조건을 완화하는 것은 내가 계약한 금액보다 더 적은 금액을 주겠다는 의미임
- 사후조건 완화는 리스코프 치환 원칙 위반임
- 사후조건 강화는 리스코프 치환 원칙 위반하지 않음

#### 슈퍼타입의 불변식은 서브타입에서도 반드시 유지돼야 한다
- 모든 객체는 생성 직후부터 소멸까지 불변식을 만족시켜야 함
- 자식 클래스가 계약을 위반할 수 있는 코드를 작성하는 것을 막을 수 있는 유일한 방법은 인스턴스 변수의 가시성을 protected가 아니라 private으로 만드는 것뿐임 -> 캡슐화의 중요성
- 자식 클래스의 인스터스 변수의 상태를 변경하고 싶으면 부모 클래스에 protected 메서드를 제공하고 이 메서드를 통해 불변식을 체크하게 해야함

### 가변성 규칙
#### 서브타입은 슈퍼타입이 발생시키는 예외와 다른 타입의 예외를 발생시켜서는 안 된다
- 부모 클래스가 던지는 예외가 속한 상속 계층이 아니라 다른 상속 계층에 속하는 예외를 던질 경우 자식 클래스는 부모 클래스를 대체할 수 없음 -> 서브타입이 아님
- 자식 클래스가 부모 클래스가 하는 일보다 더 적은 일을 수행하는 것은 자식 클래스는 부모 클래스와 동일하지 않다는 의미임

#### 서브타입의 리턴 타입은 공변성을 가져야 한다
- 공변성(covariance)
    - S와 T 사이의 서브타입 관계가 그대로 유지됨
    - 해당 위치에서 서브타입인 S가 슈퍼타입인 T 대신 사용될 수 있음
    - 리스코프 치환 원칙은 공변성과 관련된 원칙임
- 반공변성(contravariance)
    - S와 T 사이의 서브타입 관계가 역전됨
    - 슈퍼타입인 T가 서브타입인 S 대신 사용될 수 있음
- 무공변성(invariance)
    - S와 T 사이의 아무런 관계도 존재하지 않음
    - S 대신 T를 사용하거나 T 대신 S를 사용할 수 없음
- 부모 클래스에서 구현된 메서드를 자식 클래스에서 오버라이딩할 때 부모 클래스에서 선언한 반환타입의 서브타입으로 지정할 수 있는 특성을 리턴 타입 공변성(return type covariance)이라고 부름
- 리턴 타입 공변성이란 메서드를 구현한 클래스의 타입 계층 방향과 리턴 타입의 타입 계층 방향이 동일한 경우를 가리킴

#### 서브타입의 메서드 파라미터는 반공변성을 가져야 한다
- 부모 클래스에서 구현된 메서드를 자식 클래스에서 오버라이딩할 때 파라미터 타입을 부모 클래스에서 사용한 파라미터의 슈퍼타입으로 지정할 수 있는 특성을 파라미터 타입 반공변성(parametertypecontravariance)이라고 부름
- 파라미터 타입 반공변성이란 메서드를 정의한 클래스의 타입 계층과 파라미터의 타입 계층의 방향이 반대인 경우 서브타입 관계를 만족한다는 것을 의미함
- 반공변성은 제네릭 프로그래밍을 공부하는 데 도움이 됨

### 함수 타입과 서브타이핑
- 익명 함수(anonymous function), 함수 리터럴(function literal), 람다 표현식(lambda expression) = 이름 없는 메서드
- 함수 타입의 서브타입을 정의할 수 있음
- 서브타입이 슈퍼타입을 치환할 수 있다는 것은 계약에 의한 설계에서 정의한 계약 규칙과 가변성규칙을 준수한다는 것을 의미함
- 진정한 서브타이핑 관계를 만들고 싶다면 
    - 서브타입에 더 강력한 사전조건이나 더 완화된 사후조건을 정의해서는 안됨
    - 슈퍼타입의 불변식을 유지하기 위해 항상 노력해야 함
    - 서브타입에서 슈퍼타입에서 정의하지 않은 예외를 던져서는 안됨

# 읽고 느낀 점
- 리스코프 치환 원칙에 대해 깊게 생각해본 계기가 된 것 같다
- 특히 사전조건 사후조건에 대해 알게 되었다
- 리스코프 치환 원칙이 단순히 자식클래스가 부모클래스를 대체할 수 있다는 것이 아니며 단순히 interface를 상속받았다고 해서 리스코프 치환 원칙을 만족한다는 의미가 아닌 것도 알게되었다
- 계약이라는 의미로 리스코프 치환 원칙을 바라보니 사전조건, 사후조건을 이해하기 쉬웠다