# 간략한 내용 정리
## 계약에 의한 설계
구현이 복잡하고 부수 효과를 가진 다수의 메서드들을 연이어 호출하는 코드를 분석하는 경우엔 실행 결과를 예측하기가 어렵습니다. 
캡슐화의 가치는 사라지고 개발자는 복잡하게 얽히고 설킨 로직을 이해하기 위해 코드의 구석구석을 파헤쳐야만 합니다. 

인터페이스만으로는 객체의 행동에 관한 다양한 관점을 전달하기 어려움이 있기에 명령의 부수효과를 쉽고 명확하게 표현할 수 있느 커뮤니케이션 수단으로 **계약에 의한 설계**가 주는 혜택을 확인할 필요가 있습니다. 

계약에 의한 설계를 사용하면 협력에 필요한 다양한 제약과 부수효과를 명시적으로 정의하고 문서화할 수 있습니다. 
클라이언트 개발자는 오퍼레이션 구현을 살펴보지 않더라도 객체의 사용법을 쉽게 이해할 수 있습니다. 
계약은 실행 가능하기에 구현에 동기화돼 있는지 여부를 런타임으로 검증할 수 있습니다. 
따라서 주석과는 다르게 시간의 흐름에 뒤쳐질 필요가 없습니다. 

이번장에서 중요한 것은 코드가 아닌 개념입니다. 
코드를 구현하는 방법보단 계약에 의한 설계를 사용하는 이유와 장점을 이해하는 것이 이번장의 목표입니다. 

### 협력과 계약
#### 부수효과를 명시적으로 
메시지의 이름과 파라미터 목록은 시그니처를 통해 전달할 수 있지만 협력을 위해 필요한 약속과 제약을 인터페이스를 통해 전달할 수 없기에, 협력과 관련된 상당한 내용이 암시적인 상태로 남게 됩니다.

여기서는 6장의 명령-쿼리 분리 원칙을 설명하기 위해 소개했던 일정 관리 프로그램의 C#버전을 이용해 계약에 의한 설계 개념을 설명하도록 하겠습니다. 
명령과 쿼리를 분리했기에 Event 클래스의 클라이언트는 먼저 IsSatisfied 메서드를 호출해 RecurringSchedule의 조건을 만족시키는지 여부를 확인한 후에 Reschdule 메서드를 호출해야 합니다. 

인터페이스만으로는 메서드의 순서와 관련된 제약을 설명하기 쉽지 않지만 계약에 의한 설계 라이브러리인 Code Contracts를 사용하면 IsSatisfied 메서드 실행 결과가 true 일때만 Reschdule 메서드를 호출할 수 있다는 사실을 명확하게 표현할 수 있습니다.

```c#
class Event{
	public bool IsSatisfied(RecurringSchedule schedule){...}

	public void Reschdule(RecurringSchedule schedule){
		Contract.Requires(IsSatisfied(schedule));
		...
	}
}
```
Code Contracts와 같이 계약에 의한 설계 개념을 지원하는 라이브러리나 언어들은 일반 로직과 구분할 수 있도록 제약 조건을 명시적으로 표현하는 것이 가능합니다.
위 코드에서 Contract.Requires는 메서드를 호출하기 전에 true여야 하는 조건을 표현하기 위해 Code Contracts에서 제공하는 API로 IsSatisfied 메서드의 반환값이 true일 경우에만 Reschdule 메서드를 호출할 수 있다는 사실을 명확하게 표현합니다. 

이렇듯 계약에 의한 설계를 사용하면 제약 조건을 명시적으로 표현하고 자동으로 문서화할 수 있을 뿐아니라 실행을 통해 검증할 수 있습니다.  
#### 계약
계약의 세부적인 내용은 일반적으로 다음과 같은 특성을 가집니다. 
	각 계약 당사자는 계약으로부터 **이익(benefit)** 을 기대하고 이익을 얻기 위해 **의무(obligation)** 를 이행합니다. 
	각 계약 당사자의 이익과 의무는 계약서에 **문서화** 됩니다. 

계약은 협력을 명확하게 정의하고 커뮤니케이션을 할 수 있는 범용적인 아이디어입니다. 
그리고 사함들이 협력을 위해 사용하는 계약이라는 아이디어를 객체들이 협력하는 방식에도 적용할 수 있을까라는 의문을 품은 사람이 있습니다.
### 계약에 의한 설계
버트란드 마이어는 Eiffel 언어를 만들면서 사람들 사이의 계약에 착안해 계약에 의한 설계 기법을 고안했습니다. 
버트란드 마이어가 제시한 계약의 개념은 사람들 사이의 계약과 유사합니다. 

계약에 의한 설계 개념은 **인터페이스에 대해 프로그래밍하라**는 원칙을 확장한 것입니다. 
계약에 의한 설계를 이용하면 오퍼레이션의 시그니처를 구성하는 다양한 요소들을 이용해 협력에 참여하는 객체들이 지켜야하는 제약 조건을 명시할 수 있습니다. 
이 제약 조건을 인터페이스의 일부로 만듦으로써 코드를 분석하지 않고도 인터페이스의 사용법을 이해할 수 있게 됩니다. 

```java
// 가시성 반환 타입 메서드이름 파라미터타입 이름, 파라미터타입 이름   
public Reservation reserve(Customer customer, int audienceCount){

}
```
해당 코드는 자바 코드로 구성된 reserve 메서드 입니다. 
해당 메서드를 확인하면 메서드의 이름, 매개변수의 이름을 통해 오퍼레이션이 클라이언트에게 어떤 것을 제공하는지를 충분히 설명할 수 있습니다. 

```java
public class Screening{
	private Movie movie; // not null
	private int sequence; // > =1 
	private LocalDateTime whenScreened; // after current
}
```
위의 코드는 정상적인 Screening이 되기 위해 각 인스턴스의 조건에서 movie는 not null, sequece는 1이상, whenScreened은 현재시간보다 앞서야 합니다. 
해당 조건을 만족하지 않으면 상영할 수 없어야 합니다. 

해당 조건에 따라 협력하는 객체들의 기대가 생기게 됩니다. 
서버는 자신이 처리할 수 있는 범위의 값을 클라이언트가 전달할 것임을 기대합니다. 
클라이언트는 자신이 원하는 값을 서버가 반환할 것이라 예상합니다.
클라이언트는 메시지 전송 전과 후의 서버의 상태가 정상일 것이라 기대합니다. 

이 요소들을 순서대로 사전조건, 사후조건, 불변식이라고 부릅니다. 
그리고 해당 3가지 조건은 계약에 의한 설계 개념을 지원하는 언어로 구현할 수 있는데 아쉽게도 자바는 구현할 수 없어 앞으로 C#으로 작성된 예제를 살펴볼 것입니다.
#### 사전조건
사전 조건이란 메서드가 정상적으로 실행되기 위해 만족해야하는 조건입니다. 
만약 사전 조건을 만족시키지 못한 경우 클라이언트에 버그가 있다는 것을 의미합니다. 

```C#
public Reservation Reserve(Customer customer, int audienceCount){
	Contract.Requires(customer != null);
	Contract.Requires(audienceCount >= 1);
	return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
}
```
해당 코드는 Reserve메서드를 실행하기 앞서 사전 조건을 만족시키지 못할 경우 클라이언트에게 버그가 있다는 사실을 알리게 됩니다. 

```C#
var reservation = screnning.Reserve(null, 2);
```
예를 들어 위와 같은 코드로 테스트를 하게 될 경우 ContractException 예외가 발생하게 됩니다. 

해당 코드들을 통해 계약에 의한 설계를 사용하면 계약만을 위해 준비된 전용 표기법을 사용해 계약을 명확히 표현할 수 있으며, 계약을 인반 로직과 분리해 좀 더 강조할 수 있게 됩니다.
#### 사후조건
사후 조건은 메서드의 실행 결과가 올바른지를 검사하고 실행 후 객체가 유효한 상태로 남아 있는지를 검증합니다. 
일반적으로 사후 조건은 3가지 용도로 사용됩니다. 
	1. 인스턴스 변수의 상태가 올바른지 서술하기 위해 
	2. 메서드에 전달된 파라미터의 값이 올바르게 변겨됐는지 서술하기 위해 
	3. 반환값이 올바른지 서술하기 위해

다음의 2가지 이유로 사후 조건은 사전 조건보다 정의하는 것이 어려울 수 있습니다.
	1. 한 메서드 안에서 return문이 여러번 나올 경우
	2. 실행 전과 실행 후의 값을 비교해야 하는 경우 
#### 불변식
사전 조건과 사후 조건은 각 메서드마다 달자지는 데 반해 불변식은 인스턴스 생명 주기 전반에 걸쳐 지켜야 하는 규칙을 명세합니다. 
일반적으로 불변식은 객체의 내부 상태와 관련이 있습니다. 

불변식은 다음과 같은 특징을 가집니다. 
	1. 불변식은 클래스의 모든 인스턴스가 생성된 후에 만족돼야 합니다. 이것은 클래스에 정의된 모든 생성자는 불변식을 준수해야 한다는 것을 의미합니다. 
	2. 불변식은 클라이언트에 의해 호출 가능한 모든 메서드에 의해 준수되어야 합니다. 메서드가 실행되는 중에는 객체의 상태가 불안정한 상태로 빠질 수 있기에 불변식을 만족시킬 필요는 없지만 메서드 실행전과 후에는 항상 불변식을 만족하는 상태가 유지되어야 합니다. 
### 계약에 의한 설계와 서브타이핑
#### 계약 규칙
핸드폰 과금 시스템에선 RatePolicy는 그림 A.3에서 알 수 있는 것처럼 기본 정책과 부가 정책을 구현하는 모든 객체들이 실체화해야하는 인터페이스입니다. 
```java
public interface RatePolicy{
	Money calculateFee(List<Call> calls);
}
```

<img src="https://github.com/OiKimiO/object/assets/53805469/e551e030-c72c-4255-aca1-e75365b1e23e" style="width:100%;" />
해당 클래스들이 RatePolicy의 서브타입인지를 확인하기 위해 RatePolicy 인터페이스의 구현 클래스들이 RatePolicy의 클라이언트인 Phone과 체결한 계약을 준수하는지 확인해봐야 합니다. 

이해를 돕기위해 요금 청구서를 발행하는 publishBill 메서드를 Phone에 추가하겠습니다. 
청구서의 개념을 구현한 Bill 클래스는 요금 청구의 대상인 핸드폰(phone)과 요금(fee)을 인스턴스 변수에 포함합니다. 
```java
public class Bill{
	private Phone phone;
	private Money fee;

	public Bill(Phone phone, Money fee){
		if(phone == null){
			throw new IllegalArgumentException();
		}

		if(fee.isLessThan(Money.ZERO)){
			throw new IllegalArgumentException();
		}

		this.phone = phone;
		this.fee = fee;
	}
}
```

Phone 클래스의 publish 메서드는 가입자에게 청구할 요금을 담은 Bill 인스턴스를 생성한 후 반환합니다. 

```java
public class Phone{
	private RatePolicy ratePolicy;
	private List<Call> calls = new ArrayList<>();

	public Phone(RatePolicy ratePolicy){
		this.ratePolicy = ratePolicy;
	}

	public void call(Call call){
		calls.add(call);
	}

	public Bill publishBill(){
		return new Bill(this, ratePolicy.calculateFee(calls));
	}
}
```
publishBill 메서드에서 청구서의 요금은 최소 0원보다 크거나 같아야 함으로 calculateFee의 반환값은 0원보다 커야하며, 다음과 같이 사후 조건을 정의할 수 있습니다. 

```java
assert result.isGreaterThanOrEqual(Money.ZERO);
```

또한 calculateFee 오퍼레이션은 파라미터로 전달된 Call 목록에 대한 요금의 총합을 계산합니다. 이를 위해선 파라미터인 calls가 null이 아니어야하여 사전조건을 다음과 같이 정의할 수 있습니다.
```java
assert calls != null;
```

사전 조건과 사후조건을 각각 기본정책과 부가정책에 추가하면 다음과 같습니다.

```java
public abstract class BasicRatePolicy implements RatePolicy{
	@Override
	public Money calculateFee(List<Call> calls){
		// 사전 조건
		assert calls != null;

		Money result = Money.ZERO;

		for(Call call: calls){
			result.plus(calculateCallFee(call));
		}

		// 사후 조건
		assert result.isGreaterThanOrEqual(Money.ZERO);
		
		return result;
	}

	protected abstract Money calculateCallFee(Call call);
}

public abstract class AdditionalRatePolicy implements RatePolicy{
	private RatePolicy next;

	public AddtionalRatePolicy(RatePolicy next){
		this.next = next;
	}

	@Override
	public Money calculateFee(List<Call> calls){
		// 사전 조건
		assert calls != null;

		Money fee = next.calculateFee(calls);
		Money result = afterCalculated(fee);

		// 사후 조건
		assert result.isGreaterThanOrEqual(Money.ZERO);
		
		return result;
	}

	protected abstract Money afterCalculated(Money fee);
}

```

다음은 이 예제를 이용해 계약 규칙과 가변성 규칙에 대해 설명하고자 합니다.
##### 서브타입에 더 강력한 사전조건을 정의할 수 없다
한번도 통화가 발생하지 않은 Phone에 대한 청구서를 발행하는 시나리오를 고려해봅니다 
```java
Phone phone = new Phone(new RegularPolicy(Money.wons(100), Duration.ofSeconds(10)));
Bill bill = phone.publishBill();
```
Phone의 코드를 보면 내부적으로 calls를 빈 리스트로 초기화합니다. 
따라서 위의 코드처럼 calls를 호출하지 않으면 RatePolicy의 calculateFee 메서드 인자로 빈 리스트가 전달될 것입니다. 
그렇지만 해당 코드는 calls는 null인 경우가 아니므로  calculateFee의 사전 조건은 만족합니다. 

하지만 RegularPolicy의 부모 클래스인 BasicRatePolicydp calls가 빈 리스트여서는 안된다는 사전 조건을 추가한다면 정상적으로 처리되지 않게 됩니다. 

```java
public abstract class BasicRatePolicy implements RatePolicy{
	@Override
	public Money calculateFee(List<Call> calls){
		// 사전 조건
		assert calls != null;
		assert !calls.isEmpty();
		...
	}
}
```

문제는 여기서 발생하는데 기존의 클라이언트인 Phone은 RatePolicy인 서버와의 계약에 있어 calls가 빈리스트여도 처리가 된다는 것으로 인지하고 있지만 현재의 코드는 그렇지 않습니다.
해당 문제로 인해 BasicRatePolicy는 RatePolicy를 대체할 수 없게되고 리스코프 치환 원칙을 위반하게 됩니다. 

반대로 사전 조건을 완화하는 입장에선 문제가 발생하지 않습니다.
##### 서브타입에 더 완화된 사후조건을 정의할 수 없다
```java
Phone phone = new Phone(new RateDiscountablePolicy(Money.wons(1000),
												   new RegularPolicy(Money.wons(100),
																Duration.ofSeconds(10)
																	)
												   )
						);

phone.call(new Call(LocalDateTime.of(2017,1,1,10,10),
				    LocalDateTime.of(2017,1,1,10,11)
				    )
		   );

Bill bill = phone.publishBill();
```
해당 코드를 확인하면 통화시간은 1분이라 통화 요금이 600원이 적용되었지만 요금 할인으로 인해 1000원 할인을 받게 됩니다.
그래서 최종 총구 금액이 -400원이 되는데, 현재는 서버인 RatePolicy에선 총구 금액이 0보다 작을 경우 받을 수 없다는 사후 조건이 걸려 있습니다. 

하지만 이러한 내용을 완하해 정상적으로 실행할 수 있게 만든다면 다음과 같은 문제가 발생합니다. 

```java
public class Bill{
	public Bill(Phone phone, Money fee){
		if(fee.isLessThan(Money.ZERO)){
			throw new IllegalArgumentException();
		}
	} 
}
```

기존의 Phone과 RatePolicy 사이의 계약에 의해 RatePolicy가 이행되야 할 의무가 제대로 실행되지 않고 Bill에서 문제가 발생합니다. 
이렇게 했을 때 가장 큰 문제는 에러가 발생했을 때 Bill에 문제가 있다고 말한다는 것입니다. 

결국 Phone과 RatePolicy 사이의 계약이 지켜지지 못하며 리스코프 치환 원칙을 위반하게 됩니다. 

반대로 사후 조건을 강화하는 계약에 영향을 미치진 않습니다.
##### 슈퍼타입의 불변식은 서브타입에서도 반드시 유지돼야 한다
불변식은 메서드가 실행되기 전과 후에 반드시 만족시켜야 하는 조건입니다. 
하지만 메서드가 실행중엔 만족시키지 않아도 무방하며, 생성자 시작시점엔 만족 시키지 못하더라도 종료 시점엔 만족시켜야 합니다. 

```java
public abstract class AdditionalRatePolicy implements RatePolicy{
	protected RatePolicy next;

	public AddtionalRatePolicy(RatePolicy next){
		this.next = next;
		
		// 불변식
		assert next != null;
	}

	@Override
	public Money calculateFee(List<Call> calls){
		// 불변식
		assert next != null;
		
		// 사전 조건
		assert calls != null;

		Money fee = next.calculateFee(calls);
		Money result = afterCalculated(fee);

		// 사후 조건
		assert result.isGreaterThanOrEqual(Money.ZERO);
		
		// 불변식
		assert next != null;
		
		return result;
	}

	protected abstract Money afterCalculated(Money fee);
}

public class RateDiscountablePolicy extends AdditionalRatePolicy{
	public void changeNext(RatePolicy next){
		this.next = next;
	}
}
```
위의 코드는 불변식을 유지할 수 없습니다.
왜냐하면 AdditionalRatePolicy의 자식 클래스인 RateDiscountablePolicy가 AdditionalRatePolicy의 RatePolicy를 변경할 수 있도록 접근제어자가 protected로 설정되어 있기 때문입니다. 

```java
RateDiscountPolicy policy = new RateDiscountPolicy(Money.wons(1000),
												   new RegularPolicy(Money.wons(100),
													          Duration.ofSeconds(10)
																    )
												   );

policy.changeNext(null); // 불변식 위반 
```

위의 코드처럼 RateDiscountPolicy가 AdditionalRatePolicy의 RatePolicy를 변경할 수 있게 되면 불변식이 유지가 되지 않습니다. 
그래서 AdditionalRatePolicy의 RatePolicy의 접근 제어자를 protected가 아닌 private으로 변경할 필요가 있습니다.

이렇게 했을때 캡슐화가 잘 유지되게 됩니다.

#### 가변성 규칙
##### 서브타입은 슈퍼타입이 발생시키는 예외와 다른 타입의 예외를 발생시켜서는 안 된다.
RatePolicy의 calculateFee 오퍼레이션 인자로 빈 리스트를 전달받았을 때 EmptyCallException 예외를 던지도록 수정해보겠습니다.

```java
public class EmptyCallException extends RuntimeException{...}

public interface RatePolicy{
	Money calculateFee(List<Call> calls) throws EmptyCallException;
}

public abstract class BasicRatePolicy implements RatePolicy{
	@Override
	public Money calculateFee(List<Call> calls){
		if(calls == null || calls.isEmpty()){
			throw new EmptyCallException();
		}
	}
}
```

해당 코드에서 RatePolicy와 협력하는 메서드가 다음과 같이 있다고 가정해보겠습니다. 

```java
public void calculate(RatePolicy policy, List<Call> calls){
	try{
		return policy.calculateFee(calls);
	} catch(EmptyCallException ex){
		return Money.ZERO;
	}
}
```

그리고 RatePolicy를 구현하는 AdditionalRatePolicy 클래스가 다른 예외를 던진다고 가정해보겠습니다. 

```java
public abstract class AdditionalRatePolicy implements RatePolicy{
	@Override
	public Money calculateFee(List<Call> calls){
		if(calls == null || calls.isEmpty()){
			throw new NoneElementException();
		}
	}
}
```

만약 NoneElementException 클래스가 EmptyCallException 클래스의 자식 클래스라면 AdditionalRatePolicy는 RatePolicy를 대체할 수 있습니다. 
하지만 반대로 자식이 아니라면 AdditionalRatePolicy는 RatePolicy를 대체할 수 없습니다. 
그 이유는 부모 클래스인 RatePoliy의 예외를 자식 클래스인 AdditionalRatePolicy가 포함하지 않기 때문입니다. 
따라서 서브타입이 아니게 됩니다. 

해당 규칙의 변형이 존재하는데 먼저 다음의 코드를 보겠습니다. 

```java
public class Bird{
	public void fly(){...}
}

public class Penguin extends Bird{
	@Override
	public void fly(){
		throw new UnSupportedOperationException();
	}
}
```

```java
public class Penguin extends Bird{
	@Override
	public void fly(){}
} 
```
해당 코드는 펭귄이 새 종류에 속하지만 날수는 없다는 코드를 보여준 예시입니다. 
그리고 두 예시는 예외를 던지느냐, 아무것도 하지 않느냐의 차이는 있지만 클라이언트 관점에서 자식 클래스가 부모 클래스가 하는 일보다 더 적은 일을 수행한다는 공통점이 있습니다. 

클라이언트의 관점에서 부모 클래스에 대해 기대했던 것보다 더 적은 일을 수행하는 자식 클래스는 부모 클래스와 동일하지 않습니다.
##### 서브타입의 리턴타입은 공변성을 가져야 한다.
상속이란 문맥안에서 공변성과 반공변성의 의미를 살펴볼 필요가 있습니다. 
먼저 공변성, 반공변성, 무공변성의 개념을 살펴본 후 서브타이핑과 공변성, 반공변성 사이의 관계를 살펴보도록 하겠습니다. 

먼저 S가 T의 서브타입이라고 가정하고 다음과 같이 개념을 나눠보도록 하겠습니다. 
###### 공변성
S와 T의 서브타입 관계가 그대로 유지되며, 이 경우 서브타입인 S는 슈퍼타입인 T를 대신 사용할 수 있게 됩니다.
###### 반공변성
S와 T 사이의 서브타입 관계가 역전되며, 이 경우 해당 위치의 슈퍼타입인 T가 서브타입인 S 대신 사용될 수 있습니다. 
###### 무공변성
S와 T사이에는 아무런 관계도 존재하지 않습니다. 따라서 S대신 T를 사용하거나 T대신 S를 사용할 수 없습니다. 

이해를 돕기위해 그림 A.4와 같이 서브타입 관계를 구현한 3개의 상속 계층을 살펴보겠습니다. 
3개의 상속 계층 모두 리스코프 치환 원칙을 만족하도록 구현돼있다고 가정하겠습니다.
<img src="https://github.com/OiKimiO/object/assets/53805469/c0a60dc5-8974-4504-948e-f280e30c446d" style="width:100%;" />
해당 설계를 코드로 표현하면 다음과 같습니다. 

해당 출판하는 출판사를 나타내는 Publisher 클래스는 독립출판사를 구현하는 IndependentPublisher 클래스의 슈퍼타입이니다. 

```java
public class Publisher{}

public class IndependentPublisher extends Publisher{}
```

Book은 Magazine의 부모 클래스입니다. Book은 책이나 잡지를 출판하는 Publisher에 대한 참조를 보관합니다. 
Magazine은 Book의 자식 클래스입니다.

```java
public class Book{
	private Publisher publisher;

	public Book(Publisher publisher){
		this.publisher = publisher;
	}
}

public class Magazine extends Book{
	public Magazine(Publisher publisher){
		super(publisher);
	}
}
```

Book과 Magazine을 판매하는 판매처는 2종류가 존재합니다. 
하나는 거리에서 책을 판매하는 가판대를 구현한 BookStall이고 다른 하나는 전문적으로 잡지를 판매하는 MagazineStore입니다. 
BookStall, MagazineStore은 독립 출판사가 출간한 Magazine만 판매할 수 있습니다.

```java
public class BookStall{
	public Book sell(IndependentPublisher independentPublisher){
		return new Book(independentPublisher);
	}
}

public class MagazineStore extends BookStall{
	@Override
	public Book sell(IndependentPublisher independentPublisher){
		return new Magazine(independentPublisher);
	}
}
```

Customer은 BootStall에게 sell 메시지를 전송해 책을 구매합니다. 

```java
public class Customer{
	private Book book;

	public void order(BookStall bookStall){
		this.book = bookStall.sell(new IndependentPublisher());
	}
}
```
##### 서브타입의 메서드 파라미터는 반공변성을 가져야 한다.