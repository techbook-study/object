# 간략한 내용 정리
8장에선 유연하고 재사용 가능한 설계를 만들기 위해 적용할 수 있는 다양한 의존성 관리 기법들을 소개했습니다. 
이번장에서는 이 기법들을 원칙이라는 관점에서 정리하며, 설계를 논의할 때 사용할 수 있는 공통의 어휘나 개념을 정리하는 시간을 가져봅니다.

### 개방-폐쇄 원칙
로버트 마틴은 확장 가능하고 변화에 유연한게 대응할 수 있는 설계를 만들 수 있는 원칙 중 하나로 개방-폐쇄 원칙을 고안했습니다. 
개방 폐쇄 원칙은 **소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려있어야하고, 수정에는 닫혀 있어야 한다**는 의미를 가지고 있습니다. 

그렇다면 애플리케이션의 동작을 확장하기 위해서 코들르 수정하지 않고도 새로운 동작을 추가할 수 있을까요?
#### 컴파일타임 의존성을 고정시키고 런타임 의존성을 변경하라
사실 개방 폐쇄 원칙은 런타임 의존성과 컴파일 의존성에 관한 이야기입니다. 
런타임 의존성은 실행시 협력에 참여하는 객체들 사이의 관계이며, 컴파일 타임 의존성은 코드에서 드러나느 클래스들 사이의 관게입니다. 
그리고 앞장에서 살펴본 것처럼 유연하고 재사용 가능한 설계에서 런타임 의존성과 컴파일 타임 의존성은 서로 다른 구조를 가집니다. 

영화 예매 시스템의 할인 정책을 의존성 관점에서 다시 살펴보면 다음과 같습니다. 
컴파일 시점엔 Movie 객체가 DiscountPolicy를 의존하고, 실행 시점엔 AmountDiscountPolicy, PercentDiscountPolicy를 의존합니다. 
이처럼 Movie는 컴파일 시점과 실행 시점의 의존성이 다르다는 것을 알 수 있습니다.

그리고 이러한 차이로 인해 새로운 코드인 NoneDiscountPolicy를 추가할 때는 기존의 코드를 수정하는 일 없이 완전히 새로운 기능을 이상없이 추가할 수 있게됩니다. 
이처럼 수정에는 닫혀있는 확장에 대해서 열려있는 코딩 방식을 개방 폐쇄 원칙이라 합니다. 

결국 개방 폐쇄 원칙은 컴파일 타임의 의존성을 수정하지 않고도 런타임 의존성을 쉽게 변경하고 수정할 수 있는 구조라는 것을 알 수 있습니다. 

#### 추상화가 핵심이다
개방 폐쇄의 원칙은 추상화에 의존하는 것입니다. 여기서 추상화와 의존이라는 두 개념 모두가 중요합니다. 

추상화란 핵심적인 부분만 남기고 불필요한 부분은 생략함으로써 복잡성을 극복하는 기법입니다. 
추상화 과정을 거치면 문맥이 바뀌더라도 변하지 않는 부분만 남게 되고 문맥에 따라 변하는 부분은 생략됩니다. 
생략된 부분은 문맥에 적합한 내용으로 채워넣음으로써 각 문맥에 적합하게 기능을 구체화하고 확장할 수 있습니다. 
또한 추상화 부분은 수정에 닫혀 있습니다.
 
이것이 추상화가 개방 폐쇄 원칙을 가능하게 만드는 이유입니다. 

```java
public abstract class DiscountPolicy{
	private List<DiscountCondition> conditions = new ArrayList<>();

	public DiscountPolicy(DiscountCondition... conditions){
		thos.conditions = Arrays.asList(conditions);
	}

	public Money calculateDiscountAmount(Screening screening){
		for(DiscountCondition each : conditions){
			if(each.isSatisfiedBy(screening)){
				return getDiscountAmount(screening);
			}
		}
		return screening.getMovieFee();
	}

	abstract protected Money getDiscountAmount(Screening screening);
}

public class Movie{
	...
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}

	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
DiscountPolicy는 할인여부를 확인하고 할인값을 리턴해주는 변하진 않는 calculateDiscountAmount 메서드와 구현체에 따라 변하는 getDiscountAmount 메서드가 존재합니다. 
그리고 DiscountPolicy를 상속받는 각 구현체들은 getDiscountAmount를 기능에 맞게 재정의하여 사용합니다.
그렇기에 DiscountPolicy는 추상 클래스라고 볼 수 있습니다.  

이렇게 추상화된 DiscountPolicy는 설계의 확장을 가능합니다. 
하지만 단순히 어떤 개념을 추상화했다고 해서 개방 폐쇄 원칙을 지킬 수 있는 것은 아닙니다. 
개방 폐쇄 원칙에서 폐쇄를 가능하게 하는 것은 의존성의 방향인데, 수정에 대한 영향을 최소화하기 위해선 모든 요소가 추상화에 의존해야 합니다. 

Movie 클래스를 보면 DiscountPolicy에만 의존하고 있는 것을 볼 수 있습니다. 
이렇게 Movie 클래스가 DiscountPolicy에만 의존하는 것을 통해 자식 클래스가 추가되더라도 영향을 받지 않는다는 것을 알 수 있습니다. 

그렇지만 모든 설계가 추상화에 의존했다고 해서 설계를 유연하게 확장할 수 있는 것은 아닙니다. 
여기서 중요한것은 변하는 것과 변하지 않는 것이 무엇인지를 이해하고 이를 추상화의 목적적으로 삼아야 합니다. 

### 생성 사용 분리
결합도가 높아질수록 개방-폐쇄 원칙을 따르는 구조를 설계하기 어려워집니다.
예를 들어 다음과 같은 코드가 있다고 가정해보겠습니다.
```java
public class Movie{
	...
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee){
		...
		this.discountPolicy = new AmountDiscountPolicy(...);
	}

	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
해당 코드는 Movie를 생성하는 컴파일 시점에 할인정책의 구현을 완료해버립니다. 
이렇게 객체가 알아야 하는 지식이 많을 수록 결합도가 높아집니다. 

그리고 결합도가 높아지는 이유는 생성과 사용이 분리되지 않아서 입니다. 
여기서 말하는 생성과 사용의 분리란 소프트웨어 시스템은 (응용 프로그램 객체를 제작하고 의존성을 서로 **연결**하는) 시작 단계와 (시작 단계 이후에 이어지는) 실행 단계를 분리해야 한다는 것입니다. 

```java
public class Movie{
	...
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}

	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}

public class Client{
	public Money getAvatarFee(){
		Movie avatar = new Movie("아바타",
								Duration.ofMinute(120),
								Money.won(10000),
								new AmountDiscountPolicy(...));
		return avatar.getFee();
	}
}
```
여기서 생성과 사용을 분리하기위해선 DiscountPolicy의 구현을 생성자에서 입력할 것이 아니라 Client에서 처리할 수 있어야 합니다. 
Movie 객체가 이전과는 다르게 discountPolicy를 인자값으로 전달받아 처리하게 됩니다.
이렇게 처리함으로써 Movie의 의존성을 추상화인 DiscountPolicy로만 제한하기에 확장에 대해서는 열려있으면서도 수정에는 닫혀있는 코드를 만들 수 있습니다.

#### FACTORY 추가하기
위의 코드를 보면 Client의 코드를 보면 Movie 인스턴스를 생성하는 동시에 getFee 메시지도 함께 전송한다는 것을 알 수 있습니다.
Client 역시 생성과 사용의 책임을 함께 지니고 있다는 것을 알 수 있습니다.

여기서 Movie를 생성하는 책임을 Client의 인스턴스를 사용할 문맥을 결정할 클라이언트로 옮지만 객체 생성과 관련된 지식은 Client와 협력하는 클라이언트에게까지 새어나가기를 원하지 않는다고 가정해보겠습니다. 

이 경우 객체 생성과 관련된 책임만 전담하는 별도의 객체를 추가하고 Client는 이 객체를 사용하도록 만들 수 있는데 이렇게 생성과 사용을 분리하기위해 객체 생성에 특화된 객체를 FACTORY라고 부릅니다. 

```java
public class Factory{
	public Movie createAvatarMovie(){
		return new Movie("아바타",
						 Duration.ofMinute(120),
						 Money.won(10000),
						 new AmountDiscountPolicy(...));
	}
}

public class Client{
	private Factory factory;

	public Client(Factory factory){
		this.factory = factory;
	}

	public Money getAvatarFee(){
		return avatar.getFee();
	}
}
```
Factory를 사용하면 Movie와 AmountDiscountPolicy를 생성하는 책임 모두를 FACTORY로 이동할 수 있습니다.
이제 Client에는 사용과 관련된 책임만 남게 되는데 하나는 FACTORY를 통해 생성된 Movie 객체를 얻기 위한 것이고, 다른 하나는 Movie를 통해 가격을 계산하기 위한 것입니다.
Client는 오직 사용과 관련된 책임만 지고 생성과 관련된 어떤 지식도 가지지 않을 수 있습니다.
#### 순수한 가공물에게 책임 할당하기
시스템을 객체로 분해하는데 크게 2가지 방식이 존재하는데, 하나는 **표현적 분해**이고 다른 하나는 **행위적 분해**를 말합니다. 
표현적 분해는 도메인에 존재하는 사물 또는 개념을 표현하는 객체들을 이용해 시스템을 분해하는 것을 말합니다. 
표현적 분해는 도메인 모델에 담겨 있는 개념과 관계를 따르며 도메인과 소프트웨어 사이의 표현적 차이를 최소화 하는 것을 목적으로 합니다. 

하지만 모든 책임을 도메인 모델에게 할당하면 낮은 응집도, 높은 결합도, 재사용 저하와 같은 심각한 문제에 봉착하게 될 수도 있습니다. 
이 경우 도메인 개념을 표현한 객체가 아닌 설계자의 편의를 위해 임의르로 만들어낸 가공의 객체에게 책임을 할당해 문제를 해결할 수 있습니다. 
이처럼 책임을 할당하기 위해 창조되는 도메인과 무관한 인공적인 객체를 PURE FABRICATION(순수 가공물)이라고 부릅니다.

어떤 행동을 추가하려고 하는데 이 행동을 책임질 마땅한 도메인이 없는 경우 PURE FABRICATION을 추가하고 이 객체에 책임을 할당하면 됩니다. 
그 결과로 추가된 PURE FABRICATION은 보통 특정한 행동을 표현하는 것이 일반적입니다. 
따라서 PURE FABRICATION은 표현적 분해보다 **행위적 분해**에 의해 생성되는 것입니다.

애플리케이션은 객체와 순수하게 창조된 가공의 객체들이 모여 자신의 역할과 책임을 다하고 조화롭게 협력하는 애플리케이션을 설계하는 것이 목표여야 합니다. 

도메인 모델에서 출발해 설계에 유연성을 추가하기 위해 책임을 이리저리 옮기다 보면 많은 PURE FABRICATION을 추가하게 됩니다.
FACTORY는 객체의 생성 책임을 할당할만한 도메인 객체가 존재하지 않을 때 선택할 수 있는PURE FABRICATION입니다. 

### 의존성 주입
의존성 해결은 컴파일 타임 의존성과 런타임 의존성의 차이점을 해소하기 위한 다양한 매커니즘을 포함합니다.
의존성 주입은 의존성을 해결하기 위해 의존성을 객체의 퍼블릭 인터페이스에 명시적으로 드러내서 외부에서 필요한 런타임 의존성을 전달할 수 있도록 만드는 방법을 포괄하는 명칭입니다. 

의존성 주입에서 의존성을 해결하는 3가지 방법이 존재하는데 각각 생성자 주입, setter 주입, 메서드 주입이 존재합니다.
먼저 생성자 주입은 객체를 생성하는 시점에 생성자를 통해 의존성을 해결하게 됩니다. 생성자 주입은 객체의 생명주기에 걸쳐 관계를 유지한다는 특징을 가집니다. 

setter 주입은 객체 생성 후 메서드를 통해 의존성을 해결합니다. setter 주입의 장점은 의존성의 대상을 런타임에 변경할 수 있다는 것입니다. 하지만 setter 주입의 단점은 객체가 올바로 생성되기 위해 어떤 의존성이 필수적인지를 명시적으로 표현할 수 없다는 것입니다.

메서드 호출 주입은 메서드 실행시 이자를 이용해 의존성을 해결합니다. 즉 메서드가 의존성이 필요로 하는 유일한 경우 사용할 수 있습니다. 
#### 숨겨진 의존성은 나쁘다
의존성 주입 외에도 의존성을 해결할 수 있는 다양한 방법이 존하는데 그중 가장 널리 사용되는 대표적인 방법이 SERVICE LOCATOR 패턴입니다. 

외부에서 객체에게 의존성을 전달하는 의존성 주입과는 다르게 SERVICE LOCATOR의 경우 객체가 직접 SERVICE LOCATOR에게 의존성을 해결해줄 것을 요청합니다. 
```java
public class Movie{
	...
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee){
		this.title = title;
		this.runningTime = runningTime;
		this.fee = fee;
		this.discountPolicy = ServieLocator.discountPolicy();
	}
}

public class ServieLocator{
	private static ServieLocator soleInstance = new ServieLocator();
	private DiscountPolicy discountPolicy;

	public static DiscountPolicy discountPolicy(){
		return soleInstance.discountPolicy;
	}

	public static void provider(DiscountPolicy discountPolicy){
		soleInstance.discountPolicy = discountPolicy;
	}

	private ServieLocator(){
	}
}

public class TestClass{
	@Test
	void ServieLocator사용(){
		ServieLocator.provide(new AmountDiscountPolicy(...));
		Movie avatar = new Movie("아바타",
								 Duration.ofMinute(120),
								 Money.won(10000));
	}

	@Test
	void ServieLocator사용안함(){
		Movie avatar = new Movie("아바타",
								 Duration.ofMinute(120),
								 Money.won(10000));
	}
}
```
위의 코드는 DiscountPolicy에 대한 의존성을 ServiceLocator에 위임합니다. 
ServiceLocator는 싱글톤 방식으로 만들고 discountPolicy를 관리하기위해 provider(DiscountPolicy discountPolicy)라는 메서드를 만듭니다.

만약 discountPolicy를 변경하기 위해선 ServiceLocator를 static하게 선언한 변수와 provide 메서드를 사용해 discountPolicy을 변경해야 합니다. 

그런데 TestClass에서  ServiceLocator사용안함 메서드를 테스트하면서 문제가 발생합니다. 
왜냐하면 Movie생성자를 만들면서 discountPolicy가 null로 처리되기 때문입니다. 

이처럼 ServieLocator 패턴을 사용한 코드의 단점은 명시적으로 의존성을 관리할 때와는 달리 눈에 보이지 않으면 문제의 발생을 찾기 어렵다는 문제가 있습니다. 
그리고 싱글톤 방식으로 만들어진 ServiceLocator 클래스는 캡슐화를 깨는 문제가 생기기도 합니다. 

왜냐하면 모든 객체들이 soleInstance 변수를 통해 discountPolicy를 변경할 수 있게되고 이를 통해 객체들간의 결합도가 높아지는 문제가 발생하기 때문입니다. 

물론 ServieLocator 패턴이 무조건 잘못된 패턴은 아닙니다. 하지만 여기서 말할 가장 핵심적인 이야기는 의존성을 최대한 명시적으로 표현하라는 것입니다. 
왜냐하면 의존성을 내부에 숨기면 숨길수록 코드를 이해하기도 수정하기도 어려워지기 때문입니다. 

### 의존성 역전 원칙
#### 추상화와 의존성 역전
```java
public class Movie{
	private AmountDiscountPolicy discountPolicy;
	...
}
```
다음의 코드의 문제는 Movie가 추상 클래스인 discountPolicy에 의존하지 않고 구현체인 AmountDiscountPolicy를 의존하고 있다는 것에서 문제가 발생합니다. 

AmountDiscountPolicy를 의존해서 발생하는 문제는 구현체를 의존함으로써 다른 할인 정책을 적용하기 위해선 새로운 코드를 추가할 수 밖에 없다는 것입니다. 
즉 재사용과 안정성이 떨어지는 문제가 발생합니다. 

그렇기에 Movie와 AmountDiscountPolicy, PercentDiscountPolicy는 각각 추상화가 된 DiscountPolicy에 의존할 필요가 있습니다. 
이렇게 함으로써 각각 재사용가능하고 안전하게 프로그램을 만들 수 있게 됩니다. 

지금까지 살펴본 내용을 정리하면 다음과 같습니다. 
1. 상위 수준의 모듈은 하위 수준의 모듈에 의존해서는 안되며, 둘 모두 추상화에 의존해야 합니다.
2. 추상화는 구체적인 사항에 의존해선 안되며, 구체적인 사항은 추상화에 의존해야 합니다. 

이를 의존성 역전 원칙이라고 부릅니다. 
의존성 역전 원칙이라 부르게 된 이유는 의존성의 방향이 전통적인 절차형 프로그래밍과는 반대방향으로 나타나기 때문입니다.

#### 의존성 역전 원칙과 패키지
의존성 역전 원칙에서 역전은 의존성의 방향뿐만 아니라 인터페이스의 소유권에도 적용이 됩니다.
객체지향 프로그래밍에선 어떤 구성 요소의 소유권을 결정하는 것은 모듈입니다. 
자바는 패키지를 이용하고, C나 C#은 네임스페이스를 이용해 모듈을 구현합니다. 

![[Pasted image 20230901072128.png]]
과거의 전통적인 모듈 구조는 다음과 같이 기능별로 패키지를 구성하게 됩니다.
하지만 이렇게 패키지를 만들었을 때 Movie와 DiscountPolicy의 코드 변경이 없을 때도 영향을 받을 수 있게 됩니다.

즉 DiscountPolicy가 포함된 패키지 안의 어떤 클래스가 수정되더라도 전체가 재배포 되어야 한다는 의미가 됩니다. 
이로 인해 이 패키지에 의존하는 Movie 클래스가 포함된 패키지 역시 재컴파일 되어야 합니다. 
Movie에 의존하는 또 다른 패키지가 있다면 컴파일은 의존성의 그래프를 타고 애플리케이션 코드 전체로 번져갈 것입니다. 

![[Pasted image 20230901074453.png]]
위와 같은 문제를 해결하기 위해 추상화를 별도의 독립적인 패키지가 아니라 클라이언트가 속한 패키지에 포함을 시켜야 합니다. 
그리고 재사용될 필요가 없는 클래스들은 별도의 독립적인 패키지에 모아야 합니다. 
이 기법을 가리켜 SEPARATED INTERFACE 패턴이라고 부릅니다.

Movie와 추상 클래스인 DiscountPolicy를 하나의 패키지로 모으는 것은 Movie를 특정한 컨텍스트로부터 완벽하게 독립시킵니다. 
Movie를 다른 컨텍스트에서 재사용하기 위해서는 단지 Movie와 DiscountPolicy가 포함된 패키지만 재사용하면 됩니다. 
새로운 할인 정책을 위해 새로운 패키지를 추가하고 새로운 DiscountPolicy의 자식 클래스를 구현하기만 하면 상위 수준의 협력 관계를 재사용할 수 있고, 불필요한 AmountDiscountPolicy, PercentDiscountPolicy 클래스는 함께 배포할 필요가 없습니다. 

따라서 의존성 역전 원칙에 따라 상위 수준의 협력 흐름을 재사용하기 위해선 추상화가 제공하는 인터페이스의 소유권 역시 역전시켜야 합니다. 
잘 설계된 객체지향 애플리케이션은 인터페이스의 소유권을 서버가 아닌 클라이언트에 위치시킵니다. 
이것은 객체지향 프레임워크의 모듈 구조를 설계하는데 가장 중요한 핵심 원칙입니다. 

### 유연성에 대한 조언
#### 유연한 설계는 유연성이 필요할 때만 옳다
유연하고 재사용가능한 설계는 런타임 의존성과 컴파일타임 의존성의 차이를 인식하고 동일한 컴파일 타임 의존성으로 부터 다양한 런타임 의존성을 만들 수 있는 코드 구조를 가진 설계를 말합니다. 

하지만 유연하고 재사용 가능한 설계가 항상 좋은 것은 아닙니다. 
설계의 미덕은 다순함과 명확함으로 부터 나오기에 유연한 설계와는 다른 길을 걷습니다.

그렇기에 불필요하게 유연성을 만들어 복잡성을 높이는 행동은 자제하고 모든 것은 필요에 맞게 유연성을 추가하는 것이 가장 좋은 방법입니다.

#### 협력과 책임이 중요하다
객체의 협력과 책임은 중요합니다. 
지금까지 클래스를 중심으로 구현한 메커니즘 관점에서 의존성을 설명했지만 설계를 유연하게 만들기 위해서는 협력에 참여하는 객체가 다른 객체에 어떤 메시지를 전송하는지를 파악하는 것이 중요합니다.

결국 의존성을 관리해야 하는 이유는 역할, 책임, 협력의 관점에서 설계가 유연하고 재사용 가능해야하기 때문입니다.
그렇기에 역할, 책임, 협력의 모습이 가장 먼저 선명하게 그릴 필요가 있으며, 만약 그 모습이 제대로 그려지지 않는다면 곧바로 의존성을 관리하는 것은 좋지 않는 방법이 될 것입니다.

# 읽고 느낀 점
책을 읽으며, 일을 하며 매번 느끼는 부분이지만 순서가 가장 중요하다는 생각을 합니다.
순서란 시스템을 파악하고 시스템에 맞는 행동을 만들고 그 행동을 수행할 책임을 지정하고 그에 맞게 역할을 만든다는 순서입니다. 
물론 여기서 중복되는 부분은 의존성을 관리하는 다양한 기법을 넣을 수 있다 생각합니다. 

무튼 성급하게 기술을 적용하고 확신을 가지기보단 충분히 생각하고 여러가지를 고려한 상태에서 구현과 관리를 하는 노력이 필요하겠다는 것을 느낄 수 있는 이번 장이었습니다. 