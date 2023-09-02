# 간략한 내용 정리
## 의존성 관리하기
잘 설계된 객체지향 애플리케이션은 작고 응집도 높은 객체로 구성됩니다. 응집도 높은 객체는 단독으로 작업을 수행하지 않고 협력을 통해 작업을 진행합니다. 
그리고 협력안에서 객체들간엔 의존성이 생기게 됩니다. 
협력을 위해선 의존성이 필요하지만 과도한 의존성은 애플리케이션을 수정하기 어렵게 만듭니다. 
객체 지향 설계의 핵심은 협력을 위한 의존성은 유지하면서도 변경을 방해하는 의존성을 제거하는 것에 있습니다. 

이번장에선 충분히 협력적이면서도 유연한 객체를 만들기 위해 의존성 관리방법을 살펴보고자 합니다. 먼저 의존성이 무엇인지 알아보는 것으로 시작합니다. 

### 의존성 이해하기
#### 변경과 의존성
어떤 객체가 협력하기 위해 다른 객체를 필요로 할 때 두 객체 사이에 의존성이 존재하는데 그 의존성은 실행 시점과 구현 시점에 서로 다른 의미를 가지게 됩니다.

**실행 시점**은 의존하는 객체가 정상적으로 동작하기 위해서 실행시 의존 대상 객체가 반드시 존재해야한다는 것을 의미합니다. 

**구현 시점**은 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다는 것을 의미합니다.

```java 
public class PeriodCondition implements DiscountCondition{
	private DayOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;

	public boolean isSatisfiedBy(Screening screening){
		return screening.getStartTime().getDayOfWeek().eqauls(dayOfWeek) &&
			startTime.compareTo(screening.getStartTime().toLocalTime()) <=0 &&
			endTime.compareTo(screening.getStartTime().toLocalTime()) >=0
	}
}
```
의존성 개념 이해를 위해 다음의 PeriodCondition 코드를 준비했습니다. 
코드에서 보면 isSatisfiedBy 메서드가 정상 작동을 하기위선 Screening이 있어야 합니다. 
이처럼 어떤 객체가 예정된 작업을 정상적으로 수행하기 위해 다른 객체를 필요로 하는 경우 두객체 사이의 의존성이 존재한다고 말합니다. 

또한 의존성의 방향성은 항상 단방향입니다. Screening이 변경되면 PeriodCondition은 영향을 받지만 그 역은 성립하지 않습니다.

설계가 변경과 관련됐던것 처럼 의존성 역시 의존성을 가지는 두 요소가 변경될 수 있습니다.

한 예시로 PeriodCondition이 가지고 있는 DayOfWeek, LocalTime들의 타입 명칭이 변경되거나 DiscountCondition의 isSatisfiedBy의 명칭이 변경될 경우 변경된 곳 뿐만 아니라 변경에 영향이 있는 PeriodCondition에서도 같이 변경을 해야합니다. 

이렇듯 의존성은 자신이 의존하는 대상이 변경될 때 함께 변경되어야 한다는 특징을 가지고 있습니다.

#### 의존성 전이
의존성은 전이될 수 있습니다. Screening의 코드를 살펴보면 Screening은 Movie, LocalDateTime, Customer에 의존한다는 사실을 알 수 있습니다. 
그리고 이러한 Screening의 의존성으로 인해 PeriodCondition 역시 Screening이 의존하는 Movie, LocalDateTime, Customer에 간접적으로 의존하게 됩니다. 

하지만 이렇게 의존을 한다고 해서 모든 경우에 의존성이 전이되는 것은 아닙니다. 의존성이 실제로 전이될지 여부는 **변경의 방향**과 **캡슐화의 정도**에 따라 달라지기때문입니다. 
예를 들어, Screening이 내부 구현을 효과적으로 캡슐화하고 있다면 PeriodCondition까지는 변경이 전파되지 않을 것입니다. 
즉 의존성 전이는 변경에 의해 영향이 널리 전파될 수 있다는 경고일 뿐입니다. 

의존성은 **직접 의존성**과 **간접 의존성**으로 나누기도 합니다. 
직접 의존성은 말 그대로 한 요소가 다른 요소에 직접 영향을 주는 경우를 말하는 것이고, 간접 의존성은 명시적으로 드러나진 않지만 의존성 전이에 의해 영향을 받는 경우를 말합니다.

의존성은 클래스뿐 아니라 변경돠 관련있는 어떤 것이라도 의존성이라는 개념을 적용할 수 있습니다. 
그 대상은 객체일 수도 모듈이나 더 큰 큐모의 실행 시스템일 수도 있습니다. 

#### 런타임 의존성과 컴파일타임 의존성
의존성과 관련해 다뤄야할 또 다른 주제는 런타임 의존성과 컴파일 타임 의존성입니다. 
먼저 런타임과 컴파일 타임에 대한 의미를 설명합니다.

런타임은 말그대로 애플리케이션이 실해되는 시점을 말하며, 컴파일 타임은 일반적으로 작성된 코드를 컴파일하는 시점을 가르키지만 문맥에 따라서는 코드 그 자체를 가르키키도 합니다.

객체 지향 애플리케이션에서 런타임의 주인공은 객체입니다. 따라서 런타임 의존성을 다루는 주제는 객체 사이의 의존성입니다. 
반면 코드 관점에서의 주인공은 클래스입니다. 따라서 컴파일 타임 의존성이 다루는 주제는 클래스 사이의 의존성입니다. 

여기서 중요한 것은 런타임 의존성과 컴파일 타임 의존성이 서로 다를 수 있습니다. 사실 유연하고 재사용한 코드를 설계하기 위해서는 두 종류의 의존성을 서로 다르게 만들어야 합니다. 

설명을 위해 영화 예매 시스템 예시로 설명해보겠습니다. 

P258 그림 8.5 코드 작성 시점의 Movie와 DiscountPolicy
그림은 Movie, DiscountPolicy, AmountDiscountPolicy, PercentDiscountPolicy에 대한 설명입니다.

여기서 중요한 것은 Movie 클래스에서 AmountDiscountPolicy, PercentDiscountPolicy 클래스로 향하는 어떤 의존성도 존재하지 않는다는 것이고, Movie 클래스의 코드를 보면 AmountDiscountPolicy, PercentDiscountPolicy에 대해선 언급조차 하지 않습니다. 
```java
public class Movie{
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
하지만 런타임 의존성을 살펴보면 상황이 완전히 달라진다는 것을 알 수 있습니다. 
금액 할인 정책을 적용하기 위해서는 AmountDiscountPolicy의 인스턴스와 협력해야 하고, 비율 할인 정책을 적용하기 위해서는 PercentDiscountPolicy의 인스턴스와 협력해야 합니다. 코드를 작성하는 시점의 Movie 클래스는 AmountDiscountPolicy, PercentDiscountPolicy 클래스를 전혀 알지 못하지만 실행시점의 Movie 인스턴스는 AmountDiscountPolicy, PercentDiscountPolicy와 협력할 수 있어야 합니다. 

위의 코드와 설명들을 보며 컴파일 시점의 의존성과 런타임 시점의 의존성은 차이가 있습니다.
그 차이는 컴파일 시점의 의존성은 AmountDiscountPolicy, PercentDiscountPolicy를 의존하지 않는다는 것이고, 런타임 시점의 의존성은 협력할 객체가 무엇인지에 따라 두 객체를 사용한다는 것입니다. 
이런 차이를 보며 컴파일타임 구조와 런타임 구조의 사이의 거리가 멀면 멀수록 설계가 유연해지고 재사용이 가능해진다는 것을 알 수 있습니다.

#### 컨텍스트 독립성
클래스가 특정한 문맥에 강하게 결합될 수록 다른 문맥에 사용하기 더 어려워집니다. 클래스가 사용될 특정한 문맥에 대한 최소한의 가정만으로 이뤄져 있다면 다른 문맥에서도 사용하기가 쉬워지는 데, 이를 컨텍스트 독립성이라고 합니다. 

위의 코드상 Movie가 DiscountPolicy만을 의존하고 AmountDiscountPolicy, PercentDiscountPolicy를 곧바로 의존하지 않고, 실행시점에 필요한 객체를 의존해 선택할 수 있게 만든 것이 컨텍스트 독립성을 잘 지킨것이라 볼 수 있습니다. 

#### 의존성 해결하기 
컴파일타임 의존성을 실행 컨텍스트에 맞는 적절한 런타임 의존성으로 교체하는 것을 의존성 해결이라고 부릅니다. 

의존성 해결을 위해 다음의 3가지 방법을 사용합니다. 
1. 객체를 생성하는 시점에 생성자를 통해 의존성을 해결
2. 객체 생성 후 setter 메서드를 통해 의존성을 해결
3. 메서드 실행시 인자를 이용해 의존성 해결
##### 객체를 생성하는 시점에 생성자를 통해 의존성을 해결
```java
Movie avatar = new Movie("아바타",
						 Duration.ofMinutes(120),
						 Money.wons(1000),
						 new AmountDiscountPolicy(...)); 

Movie starWars = new Movie("스타워즈",
						   Duration.ofMinutes(120),
						   Money.wons(1000),
						   new PercentDiscountPolicy(...));

public class Movie{
	private DiscountPolicy discountPolicy;
	... 
	
	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}

}
```
Movie 객체를 생성하는 지점에 구체적인 할인 정책을 인자값으로 전달하면서 실행 시점에 Movie 객체의 DiscountPolicy 타입의 객체를 결정합니다. 
이 방법 역시 Movie 객체에 직접적으로 할인 정책 구현체를 넣지 않았다는 것에서 의존성을 줄였다고 볼 수 있습니다. 

##### 객체 생성 후 setter 메서드를 통해 의존성을 해결
```java
Movie avatar = new Movie(...);
avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
avatar.setDiscountPolicy(new PercentDiscountPolicy(...));
```
setter를 이용한 방식은 객체를 생성한 이후에도 의존하고 있는 대상을 변경할 수 있다는 가능성을 열어 놓고 싶은 경우에 유용합니다. 
예를 들어 위의 코드를 보면 AmountDiscountPolicy, PercentDiscountPolicy를 setDiscountPolicy를 이용해 할인 정책을 변경할 수 있습니다. 
이를 통해 실행 시점에서도 할인 정책을 원하는대로 변경할 수 있습니다. 

```java
Movie avatar = new Movie(...);
avatar.calculateFee(...); // 예외 발생
avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
```
하지만 위의 방식은 의존 대상을 설정하기 전까지는 객체의 상태가 불안전 할수도 있다는 문제점이 있습니다.  
그래서 가장 선호 되는 방식은 생성자에 할인 정책 인자를 추가하는 것과 할인 정책을 변경할 수 있는 메서드를 추가하는 것입니다. 
이렇게 되면 예외가 발생하는 문제를 해결할 수 있습니다. 

##### 메서드 실행시 인자를 이용해 의존성 해결
이 방식은 협력 대상에 대해 지속적으로 의존 관계를 맺을 필요 업시 메서드가 실행되는 동안만 일시적으로 의존관계가 존재해도 무방하거나, 메서드가 실행될 때마다 의존 대상이 매번 달라져야 하는 경우 유용합니다. 

### 유연한 설계
설계를 유연하고 재사용 가능하게 만들기로 결정했다면 의존성을 관리하는데 유용한 몇가지 원칙과 기법을 익힐 필요가 있습니다. 먼저 의존성과 결합도의 관계를 살펴보는 것으로 시작하겠습니다. 

#### 의존성과 결합도 
객체 지향 패러다임의 근간은 협력입니다. 객체들은 협력을 통해 애플리케이션의 생명력을 불어넣습니다. 그리고 이러한 협력은 객체간의 의존성을 낳습니다.
하지만 이러한 협력이 과하면 문제가 생기게 됩니다. 

예를 들어, Movie가 PercentDiscountPolicy와 강하게 결합된다는 가정을 해보겠습니다. 
당연히 협력이라는 관점에서 영화는 요금 계산을 위해 할인 정책을 적용할 수 있습니다. 하지만 지금의 경우 PercentDiscountPolicy만을 사용할 수 있다는 문제가 있습니다. 
 
그래서 다른 할인 정책을 사용하기 위해선 기존의 코드들을 수정하게 되고 이는 불안전한 시스템을 만드는 원인이 됩니다.
이처럼 바람직하지 못한 의존성을 갖춘 시스템을 결합도가 높은 시스템이라 불립니다.

결론적으로 바람직한 시스템을 만들기 위해선 협력을 위해 의존성을 가지되 재사용성 고려할 수 있어야 합니다. 
예를 들어, Movie가 PercentDiscountPolicy가 아닌 DiscountPolicy를 의존하여 어떤 객체를 받느냐에 따라 다양한 기능을 구현할 수 있도록 만드는 것입니다. 
이렇게 되면 재사용성 높은 시스템을 구성할 수 있으며 시스템의 안정성을 조금 더 높일 수 있습니다. 
이와 같이 바람직한 시스템을 결합도가 낮은 시스템이라고 부릅니다. 

#### 지식이 결합을 낳는다.
앞서 Movie가 PercentDiscountPolicy를 의존하면 결합도가 강하다고 표현을 했습니다. 반면 
Movie가 DiscountPolicy를 의존할 경우 결합도가 느슨하다고 표현합니다. 

여기서 결합도의 정도는 한요소가 자신이 의존하고 있는 다른 요소에 대해 알고 있는 정보의 양으로 결정됩니다. 
즉, 지식이라는 관점에서 결합도를 설명하면, 서로에 대해 알고 있는 지식의 양이 결합도를 결정하게 된다는 것입니다. 

결국 의존하는 객체에 대한 지식이 많을 수록 결합도는 높아지고, 지식이 적을 수록 결합도가 느슨해집니다.
그런데 이렇게 의존하는 객체에 대한 지식이 많아질 수록 컨텍스트간에 재사용성이 낮아지는 문제도 발생합니다. 
그렇기에 결합도를 느슨하게 만들어야 하는데 결합도를 느슨하게 만드는 방법은 추상화를 이용하는 것입니다. 

#### 추상화에 의존하라
추상화는 어떤 양상, 세부사항, 구조를 좀 더 명확하게 이해하기 위해 특정 절차나 물체를 의도적으로 생략하거나 감춤으로써 복잡도를 극복하는 방법입니다. 
추상화를 사용하면 대상에 대해 알아야 하는 지식의 양을 줄일 수 있기에 결합도를 느슨하게 유지할 수 있습니다. 

다음의 목록은 아래쪽으로 갈수록 클라이언트가 알아야 하는 지식의 양이 적어지기에 결합도가 느슨해집니다. 
##### 구체 클래스 의존성 & 추상 클래스 의존성
구체 클래스에 비해 추상 클래스는 메서드 내부 구현과 자식 클래스의 종류에 대한 지식을 클라이언트에게 숨길 수 있습니다. 
이런 특징때문에 구체 클래스보단 추상 클래스에 의존하는 것이 결합도를 낮추지만 여전히 협력하는 대상이 속한 클래스 상속 계층이 무엇인지는 알고 있어야 합니다.

##### 인터페이스 의존성
인터페이스에 의존하면 상속 계층을 모르더라도 협력이 가능해집니다. 인터페이스는 협력하는 객체가 어떤 메시지를 수신할 수 있는지에 대한 지식만 남기기에 추상 클래스 의존성보다 결합도가 낮습니다.
이러한 인터페이스 의존성은 동일한 메시지를 수신할 수 있도록 해 컨텍스트를 확장하는 것을 가능하게 합니다.
#### 명시적인 의존성
```java
public class Movie{
	private DiscountPolicy discountPolicy;
	... 
	
	public Movie(String title, Duration runningTime, Money fee){
		...
		this.discountPolicy = new AmountDiscountPolicy(...);
	}

}
```
다음의 코드는 Movie객체의 discountPolicy의 인자로 전달받지 않고 곧바로 생성자에서 AmountDiscountPolicy 구현체를 적용했습니다.

이렇게 코드를 만들 경우 Movie가 DiscountPolicy, AmountDiscountPolicy를 의존하게 됩니다. 
여기서 Movie를 호출하는 코드에선 DiscountPolicy가 어떻게 사용되는지 모르며, Movie를 사용하는 객체는 Movie의 의존성 전체를 다 파악할 수 없게 됩니다.
이처럼 의존성이 숨겨져 있는 형태를 숨겨진 의존성이라 합니다.

그런데 이런 숨겨진 의존성의 문제는 의존성을 파악하기 위해 내부 구현을 직접 들여다 볼수 밖에 없다는 문제를 가집니다.
그렇기에 의존성을 명시적으로 표현해 코드의 의존성을 명확히 드러낼 필요가 있습니다. 

```java
public class Movie{
	private DiscountPolicy discountPolicy;
	... 
	
	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}

}
```
해당 코드는 생성자를 통해 discountPolicy를 통해 의존성을 입력받은 코드입니다. 이외에도 setter를 통해 의존성을 주입받을 수 있는데 이런 형태를 통해 코드의 의존 관계를 명확히 파악할 수 있게 됩니다. 

이렇게 명시적으로 드러낸 의존성을 통해 유연하고 재사용 가능한 설계를 할 수 있게 됩니다.

#### new는 해롭다
대부분의 언어에서 클래스의 인스턴스를 생성할 수 있는 new 연산자를 제공합니다. 하지만 new를 잘못 사용하면 클래스 사이의 결합도가 극단적으로 높아집니다. 
결합도 측면에서 new가 해로운 이유는 크게 2가지 입니다

1. new 연산자를 사용하기 위해 구체 클래스의 이름을 직접 기술해야 하며, 이로 인해 new 를 사용하는 사용자는 추상화가 아닌 구체 클래스에 의존할 수 밖에 없어 결합도가 높아집니다.
2. new 연산자는 구체 클래스뿐 아니라 어떤 인자를 호출하는지도 알야아합니다. 그래서 클라이언트가 알아야 할 지식의 양이 늘어납니다. 

```java 
public class Movie{
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee){
		...
		this.discountPolicy = new AmountDiscountPolicy(Money.wons(800),
														new SequenceCondition(1),
														new SequenceCondition(10),
														new PeriodCondition(...),
														new PeriodCondition(...)
								);
	}
}
```
이처럼 Movie 객체에서 new 생성자를 이용해 구현체인 AmountDiscountPolicy를 입력하면 AmountDiscountPolicy뿐 아니라 AmountDiscountPolicy의 인자에 대한 의존성까지 늘어납니다. 
결론적으로 구현체와 인자값의 new 생성자들과 관련해 결합도가 높아지고 이는 특정 기능만을 사용할 수 있게 됩니다. 
결국 이러한 설계는 유연하지 못하게만들고, 재사용 가능성을 낮추게 됩니다. 

```java 
public class Movie{
	private DiscountPolicy discountPolicy;

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}
}

public class MovieTest{
	@Test
	void 생성자테스트(){
		Movie avater = new Movie(...,
								  new AmountDiscountPolicy(Money.wons(800),
															new SequenceCondition(1),
															new SequenceCondition(10),
															new PeriodCondition(...),
															new PeriodCondition(...))
									);
	}
}
```
위의 코드는 Movie 객체가 discountPolicy를 명시적으로 드러내고, 클라이언트가 명시적으로 구현체의 인자값을 전달합니다. 
명시적으로 의존성을 전달하는 과정을 통해 하나의 구현체에 의존하지 않고 실행시점에 따라 유연하게 인스턴스를 처리할 수 있게 됩니다. 

이처럼 사용과 생성의 책임을 분리함으로써 의존성을 생성자에 명시적으로 드러내고, 구체 클래스가 아닌 추상 클래스에 의존하게 만듦으로써 설계를 유연하게 만들 수 있습니다.

#### 가끔은 생성해도 무방하다
클래스 안에서 객체의 인스턴스를 생성하는 방식이 유용한 경우도 있습니다. 
예를 들어, Movie의 경우 AmountDiscountPolicy와 자주 협력하고 PercentDiscountPolicy와는 자주 협력하지 않는다고 가정해보겠습니다. 

```java 
public class Movie{
	private DiscountPolicy discountPolicy;
	
	public Movie(String title, Duration runningTime, Money fee){
		this(title, runningTime, fee, new AmountDiscountPolicy(...));
	}

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}
}
```
위와 같은 상황이라면 기본적으로 Movie의 생성자를 2개를 만듭니다.
하나는 기본 생성자의 인자를 넣었을 떄 AmountDiscountPolicy를 사용하게 하고 나머지는 의존성을 명시적으로 표현함으로써 discountPolicy를 인자값으로 전달받게 하는 것입니다. 
이렇게 함으로써 자주 사용되는 중복되는 코드를 줄일 수 있고 필요에 따라 유연하게 코드를 사용할 수 있게 됩니다. 

이런 방식이 존재하지만 그래도 가급적 구체 클래스의 의존성을 제거할 수 있는 방법을 찾아보는 것이 좋습니다. 
종종 모든 결합도가 모이는 새로운 클래스를 추가함으로써 사용성과 유연성이라는 두마리 토끼를 잡을 수 있는데 이어지는 9장의 FACTORY가 바로 그런 경우라 볼 수 있습니다. 

#### 표준 클래스에 대한 의존은 해롭지 않다.
의존성이 불편한 이유는 항상 변경에 대한 영향을 암시하기 때문입니다. 
따라서 변경될 확률이 거의 없는 클래스라면 의존성이 문제가 되지 않는데, 자바라면 JDK 표준 클래스가 이 부류에 속하게 됩니다. 

```java
public abstract class DiscountPolicy{
	private List<DiscountCondition> conditions = new ArrayList<>();
}
```
예를 들어, JDK 표준 컬렉션 라이브러리에 속하는 ArrayList의 경우엔 다음과 같이 직접 인스턴스를 생성해도 문제가 되지 않습니다. 왜냐하면 ArrayList가 수정될 확률이 0에 가깝기 때문입니다. 

비록 클래스를 직접 생성하더라도 가능한 한 추상적인 타입을 사용하는 것이 확장성 측면에선 유리합니다. 위의 코드에서 conditions의 타입으로 인터페이스인 List를 사용한 것은 이 때문입니다. 

이렇게 하면 다양한 List 타입의 객체로 conditions를 대체할 수 있게 설계의 유연성을 높일 수 있습니다. 
따라서 의존성에 의한 영향이 적은 경우에도 추상화에 의존하고 의존성을 명시적으로 드러내는 것은 좋은 습관입니다. 

#### 컨텍스트 확장하기
지금까지 Movie의 설계가 유연하고 재사용 가능한 이유에 대해 장황하게 설명했습니다.
이제 실제로 Movie가 유연한다는 사실을 입증하기 위해 지금까지와는 다른 컨텍스트에서 Movie를 확장해 재사용하는 2가지 예를 살펴 보겠습니다. 

##### 할인 혜택을 적용하지 않는 영화 예매 요금을 계산
```java 
public class Movie{
	private DiscountPolicy discountPolicy;
	
	public Movie(String title, Duration runningTime, Money fee){
		this(title, runningTime, fee, null);
	}

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
		...
		this.discountPolicy = discountPolicy;
	}
}
```
위의 코드처럼 할인 혜택을 적용하지 않는 영화의 경우 DiscountPolicy의 인자값이 없는 생성자를 호출하고, 할인 조건이 있는 경우엔 DiscountPolicy의 인자값이 있는 생성자를 호출하면 됩니다. 
하지만 위의 방식은 내부 코드를 수정한다는 것에 있어 버그를 발생할 수 있다는 문제를 가지고 있습니다. 

```java
public class NonDiscountPolicy extends DiscountPolicy{
	@Override
	protected Money geyDiscountAmount(Screening screening){
		return Money.ZERO;
	}
}
```
그래서 이러한 문제를 해결하기 위해 NonDiscountPolicy라는 0원 반환 정책 클래스를 만들어 관리하게 합니다.

이제 외부에서 할인 혜택을 적용하지 않는 정책을 관리할 수 있게 되어 Movie의 내부 소스를 수정할 필요 없어졌습니다. 

##### 다수의 할인 정책을 중복해서 적용한 영화
여기서 말하는 중복 할인은 금액 할인 정책과 비율 할인 정책을 혼합해서 적용할 수 있는 정책을 말합니다. 
할인 정책을 중복해서 적용하기 위해서는 Movie가 하나 이상의 DiscountPolicy와 협력할 수 있어야 합니다. 

```java 
public class OverlappedDiscountPolicy extends DiscountPolicy{
	private List<DiscountPolicy> discountPolicies = new ArrayList<>();

	public OverlappedDiscountPolicy(DiscountPolicy ... discountPolicies){
		this.discountPolicies = Arrays.asList(discountPolicies);
	}

	@Override
	protected Money getDiscountAmount(Screening screening){
		Money result = Money.ZERO;
		
		for(DiscountPolicy each : discountPolicies){
			result = result.plus(each.calculateDiscountAmount(screening));
		}
		
		return result;
	}
}
```
해당 코드는 DiscountPolicy의 중복되는 클래스 관리를 OverlappedDiscountPolicy 클래스에서 처리할 수 있게합니다. 
OverlappedDiscountPolicy 클래스에서 List를 이용해 DiscountPolicy타입을 관리함으로써 굳이 Movie에 의존하지 않고도 중복된 할인 정책을 처리할 수 있도록 만들었습니다. 

이 두가지 예제에서 설계를 유연하게 만들 수 있었던 이유는 Movie가 DiscountPolicy라는 추상화에 의존하고, 생성자를 통해 DiscountPolicy에 대한 의존성을 명시적으로 드러냈으며, new와 같이 구체 클래스에 직접적으로 다뤄야 하는 책임을 Movie 외부로 옮겼기 때문입니다. 

이처럼 결합도를 낮춤으로써 얻게 되는 컨텍스트의 확장이라는 개념이 유연하고 재사용가능한 설계를 만드는 핵심입니다.

# 읽고 느낀 점
협력을 위해선 의존성이 있어야 하지만 필요한 만큼만 의존성을 갖춰야 한다는 것을 알게 되었습니다.
특히나 의존성간의 관계는 묵시적으로 표현하는 것이 아니라 명시적으로 표현하면서 필요한 객체간의 의존성을 갖추는 작업을 하는 것이 중요하다는 것을 알게 되었습니다. 
결국 묵시적인 의존성은 새로운 버그나 결함을 일으키게 될 수 있기 떄문입니다.
무튼 시스템간의 협력을 잘 파악해 객체간의 의존성을 적절히 줄 수 있는 연습도 필요하겠다는 생각을 했습니다. 