# 간략한 내용 정리
## 합성과 유연한 설계
상속에서 부모 클래스와 자식 클래스 사이의 의존성은 컴파일 타임에 해결되지만 합성에서 두 객체 사이의 의존성은 런타임에 해결됩니다. 
상속 관계는 is-a관계라 부르고, 합성 관계는 has-a관계라고 부릅니다.

상속을 이용하면 부모 클래스의 정의 대부분을 물려받아 다른 부분만 추가하거나 재정의하면 되지만, 상속을 제대로 활용하기 위해선 부모 클래스의 내부 구현에 대해 상세히 알아야 하기에 부모 클래스와 자식 클래스간의 결합도가 높아집니다.

반면 합성은 내부에 포함되는 객체 구현이 아닌 퍼블릭 인터페이스에 의존합니다. 
그래서 합성을 이용하면 객체의 내부 구현이 변경되더라도 영향을 최소화 할 수 있어 변경에 더 안정적인 코드를 얻을 수 있습니다. 
이로 인해 상속에선 구현에 의존했던 것을 합성에서는 인터페이스에 대한 의존성으로 변경할 수 있고, 이는 클래스 사이의 높은 결합도를 객체 사이의 낮은 결합도로 대체할 수 있게 합니다.

### 상속을 합성으로 변경하기 
다음은 상속을 남용할 때의 문제점 3가지입니다. 
##### 1. 불필요한 인터페이스 상속 문제
##### 2. 메서드 오버라이딩의 오작용 문제
##### 3. 부모 클래스와 자식 클래스의 동시 수정 문제

상속으로 인한 이러한 문제는 합성으로 바꾸는 것으로 해결할 수 있는데, 이는 자식 클래스에 선언된 상속 관계를 제거하고 부모 클래스의 인스턴스를 자식 클래스의 인스턴스 변수로 선언하면 됩니다. 

이제 10장에서 소개한 상속의 예제를 합성 관계로 변경해보겠습니다.
#### 불필요한 인터페이스 상속 문제: java.util.Properties와 java.util.Stack
```java
public class Properties{
	private Hashtable<String, String> properties = new Hashtable<>();

	public String setProperty(String key, String value){
		return properties.put(key, value);
	}

	public String getProperty(String key){
		return properties.get(key); 
	}
}

public class Stack<E>{
	private Vector<E> elements = new Vector<>();

	public E push(E item){
		elements.addElement(item);
		return item;
	}

	public E pop(){
		if(elements.isEmpty()){
			throw new EmptyStackException();
		}

		return elements.remove(elements.size() - 1);
	}
}
```
Properties, Stack 클래스는 기존의 상속을 통한 구현을 진행했습니다. 
그래서 자식 클래스들은 각자의 기능의 의도와는 맞지 않게 구현되는 경우가 많았습니다. 

하지만 이번에 합성을 사용하게 되면서 각 부모 클래스의 내부 구현은 알지 못한체 부모 클래스의 퍼블릭 인터페이스를 통해서만 접근을 할 수 있습니다. 
이 과정을 통해 부모 클래스의 결합도가 줄어들고 각 Properties, Stack 클래스는 자신의 기능에만 충실할 수 있게 되었습니다.
#### 메서드 오버라이딩 오작용 문제: InstrumentedHashSet
InstrumentedHashSet도 같은 방법을 사용해 합성 관계로 표현할 수 있습니다.

```java
public class InstrumentedHashSet<E>{
	private int addCount = 0;
	private Set<E> set;

	public InstrumentedHashSet(Set<E> set){
		this.set = set;
	}

	public boolean add(E e){
		addCount++;
		return set.add(e);
	}

	public boolean addAll(Collection<? extends E> c){
		addCount += c.size();
		return set.addAll(c);
	}

	public int getAddCount(){
		return addCount;
	}
}
```
현재 방식은 InstrumentedHashSet의 경우에는 HashSet이 제공하는 퍼블릭 인터페이스를 그대로 제공하는 방식입니다. 
하지만 지금 방식은 불필요한 오퍼레이션들이 퍼블릭 인터페이스에 스며드는 문제가 발생합니다. 

그래서 이러한 문제를 해결하기위해 InstrumentedHashSet에서 Set 인터페이스를 사용하면 해결할 수 있습니다. 
이 방법을 통해 Set 인터페이스를 실체화하면서 내부에 HashSet의 인스턴스를 합성하면 HashSet에 대한 구현 결합도는 제거하면서도 퍼블릭 인터페이스는 그대로 유지할 수 있습니다. 

```java
public class InstrumentedHashSet<E> implements Set<E>{
	private int addCount = 0;
	private Set<E> set;

	public InstrumentedHashSet(Set<E> set){
		this.set = set;
	}

	@Override
	public boolean add(E e){
		addCount++;
		return set.add(e);
	}

	@Override
	public boolean addAll(Collection<? extends E> c){
		addCount += c.size();
		return set.addAll(c);
	}

	public int getAddCount(){
		return addCount;
	}
	...
}
```
InstrumentedHashSet의 코드를 보면 Set의 오퍼레이션을 오버라이딩한 인스턴스 메서드에서 내부의 HashSet인스턴스에게 동일한 메서드 호출을 그대로 전달한다는 것을 알 수 있습니다. 
이를 포워딩(forwarding)이라고 부르고 동일한 메서드를 호출하기 위해 추가된 메서드를 포워딩 메서드(forwarding method)라고 부릅니다. 

#### 부모 클래스와 자식 클래스의 동시 수정 문제: PersonalPlaylist
```java
public class PersonalPlaylist{
	private Playlist playlist = new Playlist();

	public void append(Song song){
		playlist.append(song);
	}

	public void remove(Song song){
		playlist.getTracks().remove(song);
		playlist.getSingers().remove(song.getSinger());
	}
}
```
안타깝게도 Playlist의 경우에는 합성으로 변경하더라도 가수별 노래 목록을 유지하기 위해 Playlist와 
PersonalPlaylist를 함께 수정해야 하는 문제가 해결되지 않습니다. 

그렇다고 하더라도 여전히 상속보단 합성을 사용하는 것이 좋은데, 향후에 Playlist의 내부 구현을 변경하더라도 파급효과를 최대한 PersonalPlaylist 내부로 캡슐화 할 수 있기 때문입니다.

##### 몽키 패치
몽키 패치란 현재 실행중인 환경에만 영향을 미치도록 지역적으로 코드를 수정하거나 확장하는 것을 가르킵니다. 
만약 Playlist의 코드를 수정할 권한이 없거나 소스 코드가 존재하지 않는다고 하더라도 몽키 패치가 지원되는 환경이라면 Playlist에 직접 remove 메서드를 추가하는 것이 가능합니다. 

Java는 언어 차원에서 몽키 패치를 지원하지 않기에 바이트코드를 직접 변환하거나 AOP를 이용해 몽키 패치를 구현할 수 있습니다. 

### 상속으로 인한 조합의 폭발적인 증가 
상속으로 인해 결합도가 높아지면 코드를 수정하는데 필요한 작업의 양이 과도하게 늘어나는 경향이 있습니다.
가장 일반적인 상황은 작은 기능들을 조합해서 더 큰 기능을 수해하는 객체를 만들어야 하는 경우입니다.

이같은 경우 2가지 문제점이 발생합니다. 
1. 하나의 기능을 추가하거나 수정하기 위해 불필요하게 많은 수의 클래스를 추가하거나 수정해야 합니다. 
2. 단일 상속만 지원하는 언어에서는 상속으로 인해 오히려 중복 코드의 양이 늘어날 수 있습니다.

합성을 사용하면 상속으로 인해 발생하는 클래스의 증가와 중복 코드 문제를 간단하게 해결할 수 있습니다. 
다음 코드를 통해 살펴 보겠습니다.
#### 기본 정책과 부가 정책 조합하기 
핸드폰 과금 시스템에 새로운 요구사항을 적용하려합니다. 
정책은 총 2가지로 구분하는데, 하나는 기본 정책이고 하나는 부가 정책입니다. 
기본정책은 일반 요금제와 심야 할인 요금제를 포함하고 있고,  부가 정책은 세금 정책과 기본 요금 할인 정책을 가지고 있습니다. 

부가 정책은 다음과 같은 특성을 가지고 있습니다. 
##### 기본 정책의 계산 결과에 적용됩니다. 
##### 선택적으로 적용할 수 있습니다. 
##### 조합이 가능합니다.
##### 부가 정책은 임의의 순서로 적용 가능합니다. 

다음은 이러한 특징을 토대로 기본 정책과 부가 정책의 조합 가능한 모든 요금 계산 순서를 나타낸것입니다.
#### 상속을 이용해서 기본 정책 구현하기
10장에서 봤던 Phone 클래스(부모 클래스)와 RegularPhone, NightlyDiscountPhone(자식 클래스)의 소스입니다.
해당 코드는 오직 기본 정책 요금만을 계산합니다.

```java
public abstract class Phone{
	private List<Call> calls = new ArrayList<>();

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result = result.plus(calculateCallFee(call));
		}
		
		return result;
	}

	abstract protected Money calculateCallFee(Call call);
}

public class RegularPhone extends Phone{
	private Money amount;
	private Duration seconds;
	
	public RegularPhone(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}

	@Override
	protected Money calculateCallFee(Call call){
		return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
	}
}

public class NightlyDiscountPhone{
	private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;
	private Money regularAmount;
	private Duration seconds;

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds){
		this.nightlyAmount = nightlyAmount;
		this.regularAmount = regularAmount;
		this.seconds = seconds;
	}

	private Money calculateCallFee(Call call){
		if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
			return nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		} 
		
		return regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
	}
}

```

#### 기본 정책에 세금 정책 조합하기
부가정책을 적용하는 메서드의 경우에도 추상 메서드로 만든 것을 구현체로 구현하는 것이 가장 빠른 방법이긴 합니다. 
하지만 부가 정책에 대한 추상 메서드를 만들고 자식 클래스에서 구현을 하는 방법은 클래스간의 기능을 제대로 분배하지 못하게 만들고 기능을 강하게 결합시킵니다. 

그래서 나온 것이 훅 메서드입니다. 
훅 메서드는 추상 메서드와 동일하게 자식 클래스에서 오버라이딩할 의도로 메서드를 추가했지만 편의를 위해 기본 구현을 제공하는 메서드를 말합니다. 
```java
public abstract class Phone{
	...
	protected Money afterCaculated(Money fee){
		return fee;
	}
	...
}

public class TaxableRegularPhone extends RegularPhone{
	private double taxRate;

	public TaxableRegularPhone(Money amount, Duration seconds, double taxRate){
		super(amount, seconds);
		this.taxRate = taxRate;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return fee.plus(fee.times(taxRate));
	}
}

public class TaxableNightlyDiscountPhone extends NightlyDiscountPhone{
	private double taxRate;

	public TaxableRegularPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate){
		super(nightlyAmount, regularAmount, seconds);
		this.taxRate = taxRate;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return fee.plus(fee.times(taxRate));
	}
}
```
위의 코드는 부가 정책을 표현하기위해 afterCaculated(Money fee)를 훅 메서드로 추가한 경우입니다. 
이렇게 만들게 되면 RegularPhone, NightlyDiscountPhone은 calculateCallFee 메서드만을 구체화해서 사용하면되고 TaxableRegularPhone, TaxableNightlyDiscountPhone는 afterCaculated 메서드를 구체화해서 사용하면 됩니다. 

하지만 이 코드에서 또한 RegularPhone, NightlyDiscountPhone의 중복 코드가 발생합니다. 
해당 코드를 보면 부모 클래스의 이름을 제외하면 대부분의 코드가 거의 동일하다는 것을 알 수 있습니다.

#### 기본 정책에 기본 요금 할인 정책 조합하기
```java
public class RateDiscountableRegularPhone extends RegularPhone{
	private Money discountAmount;

	public RateDiscountableRegularPhone(Money amount, Duration seconds, Money discountAmount){
		super(amount, seconds);
		this.discountAmount = discountAmount;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return fee.plus(discountAmount);
	}
}

public class RateDiscountableNightlyDiscountPhone extends NightlyDiscountPhone{
	private Money discountAmount;

	public RateDiscountableNightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, Money discountAmount){
		super(nightlyAmount, regularAmount, seconds);
		this.discountAmount = discountAmount;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return fee.plus(discountAmount);
	}
}
```
부가 정책의 기본 요금 할인 정책 또한 훅 메서드를 재정의하여 사용하여 구현하였습니다.
그래서 위와 같이 RateDiscountableRegularPhone, RateDiscountableNightlyDiscountPhone과 중복 코드가 발생하는 문제가 발생합니다.

이로 인해 어떤 문제가 발생하는지 알아보도록 하겠습니다.
#### 중복 코드의 덫에 걸리다
다음은 나머지 구현되지 않았던던 기본정책과 부가 정책의 조합을 클래스로 표현해보겠습니다.

```java
public class TaxableAndRateDiscountableRegularPhone extends TaxableRegularPhone{
	private Money discountAmount;

	public TaxableAndRateDiscountableRegularPhone(Money amount, Duration seconds, double taxRate, Money discountAmount){
		super(amount, seconds, taxRate);
		this.discountAmount = discountAmount;
	}

	@Override
	protected Money afterCalculated(Money fee){
		return super.afterCalculated(fee).minus(discountAmount);
	}
}

public class RateDiscountableAndTaxableRegularPhone extends RateDiscountableRegularPhone{
	private double taxRate;

	public RateDiscountableRegularPhone(Money amount, Duration seconds, Money discountAmount, double taxRate){
		super(amount, seconds, discountAmount);
		this.taxRate = taxRate;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return super.afterCaculated(fee).plus(fee.times(taxRate));
	}
}
```
TaxableRegularPhone는 기본적으로 일반 요금제 + 세금 정책을 가집니다.
여기서 TaxableRegularPhone의 기능과 일반 요금 할인정책을 구현한 것이 
TaxableAndRateDiscountableRegularPhone입니다.
TaxableAndRateDiscountableRegularPhone 구현은 TaxableRegularPhone을 상속받아 만들었습니다.

RateDiscountableRegularPhone는 기본적으로 일반 요금제 + 기본 요금 할인정책을 가집니다.
여기서 RateDiscountableRegularPhone의 기능과 세금 정책 계산을 구현한 것이RateDiscountableAndTaxableRegularPhone입니다.
RateDiscountableAndTaxableRegularPhone 구현은 RateDiscountableRegularPhone를 상속해서 만들었습니다. 

```java
public class TaxableAndRateDiscountableNightlyDiscountPhone extends TaxableNightlyDiscountPhone{
	private Money discountAmount;

	public TaxableAndRateDiscountableNightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate, Money discountAmount){
		super(nightlyAmount, regularAmount, seconds, taxRate);
		this.discountAmount = discountAmount;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return super.afterCalculated(fee).minus(discountAmount);
	}
}

public class RateDiscountableAndTaxableNightlyDiscountPhone extends RateDiscountableNightlyDiscountPhone{
	private double taxRate;

	public RateDiscountableNightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, Money discountAmount, double taxRate){
		super(nightlyAmount, regularAmount, seconds, discountAmount);
		this.taxRate = taxRate;
	}

	@Override
	protected Money afterCaculated(Money fee){
		return super.afterCaculated(fee).plus(fee.times(taxRate));
	}
}

```
TaxableNightlyDiscountPhone는 기본적으로 심야 할인 요금제 + 세금 정책을 가집니다.
여기서 TaxableNightlyDiscountPhone의 기능과 기본 요금 할인 정책을 구현한 것이TaxableAndRateDiscountableNightlyDiscountPhone입니다.
TaxableAndRateDiscountableNightlyDiscountPhone 구현은 TaxableNightlyDiscountPhone를 상속해서 만들었습니다. 

RateDiscountableNightlyDiscountPhone는 기본적으로 심야 할인 요금제 + 기본 요금 할인정책을 가집니다.
여기서 RateDiscountableNightlyDiscountPhone의 기능과 세금 정책 계산을 구현한 것이RateDiscountableAndTaxableNightlyDiscountPhone입니다.
RateDiscountableAndTaxableNightlyDiscountPhone 구현은 RateDiscountableNightlyDiscountPhone를 상속해서 만들었습니다.

그런데 만약 지금과 같은 방식으로 또 고정할인 정책을 추가한다면 새로운 클래스들이 다시 추가될 것입니다.

이처럼 상속의 남용으로 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스를 추가하는 경우를 가리켜 클래스 폭발 문제 혹은 조합의 폭발 문제라고 부릅니다. 
이런 문제가 발생한 것은 컴파일 타임에 결정된 자식 클래스와 부모 클래스 사이의 관계는 변경될 수 없기 때문입니다.

또한 클래스 폭발 문제는 새로운 기능을 추가할 때뿐만 아니라 수정을 할 때도 문제가 됩니다. 
만약 세금과 관련된 정책을 변경한다면 세금과 관련된 모든 클래스를 찾아 동일한 방식으로 변경해야 될 것입니다. 

### 합성 관계로 변경하기
기본적으로 컴파일 타임 의존성과 런타임 의존성의 거리가 멀수록 설계가 복잡해지기에 코드를 이해하기 어려워 집니다. 
그런데 편리한 설계를 만들기 위해 복잡성을 더하고 나면 원래의 설계보다 단순해지는 경우를 볼 수 있는데, 상속을 합성으로 변경한 경우가 그러합니다.

#### 기본 정책 합성하기
먼저 기본 정책과 부가 정책을 포괄하는 RatePolicy 인터페이스를 추가하겠습니다. 
RatePolicy는 Phone을 인자로 받아 계산된 요금을 반환하는 calculateFee 오퍼레이션을 포함하는 간단한 인터페이스입니다. 

```java
public interface RatePolicy{
	Money calculateFee(Phone phone);
}
```

기본 정책을 구현하면, 일반 요금제와 심야 할인 요금제는 개별 요금을 계산하는 방식을 제외한 전체 처리 로직은 거의 동일합니다. 
이 중복 코드를 담을 추상 클래스 BasicRatePolicy를 추가하도록 하겠습니다. 

```java
public abstract class BasicRatePolicy implements RatePolicy{
	@Override
	public Money calculateFee(Phone phone){
		Money result = Money.ZERO;

		for(Call call : phone.getCalls()){
			result.plus(calculateCallFee(call));
		}
		
		return result;
	}
	
	protected abstract Money calculateCallFee(Call call);
}

public class RegularPolicy extends BasicRatePolicy{
	private Money amount;
	private Duration seconds;

	public RegularPolicy(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}

	@Override
	protected Money calculateCallFee(Call call){
		return amount.times(call.getDuration().getSeconds()/seconds.getSeconds());
	}
}

public class NightlyDiscountPolicy extends BasicRatePolicy{
	private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;
	private Money regularAmount;
	private Duration seconds;

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds){
		this.nightlyAmount = nightlyAmount;
		this.regularAmount = regularAmount;
		this.seconds = seconds;
	}

	@Override
	protected Money calculateCallFee(Call call){
		if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
			return nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		}
		return regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		
	}
	
}
```
BasicRatePolicy의 코드를 상속받아 RegularPolicy, NightlyDiscountPolicy를 구현하였습니다.
BasicRatePolicy의 상속받음으로서 calculateCallFee 메서드만 재정의하여 사용하면 됩니다. 

```java
public class Phone{
	private RatePolicy ratePolicy;
	private List<Call> calls = new ArrayList<>();

	public Phone(RatePolicy ratePolicy){
		this.ratePolicy = ratePolicy;
	}

	public List<Call> getCalls(){
		return Collections.unmodifiableList(calls);
	}

	public Money calculateFee(){
		return ratePolicy.calculateFee(this);
	}
}
```
Phone은 컴파일타임 의존성을 구체적인 런타임 의존성으로 대체하기 위해 생성자를 통해 RatePolicy의 인스턴스에 대한 의존성을 주입받습니다. 

```java
Phone regularPhone = new Phone(new RegularPolicy(Money.wons(10), Duration.ofSeconds(10)));

Phone nightlyPhone = new Phone(new NightlyDiscountPolicy(Money.wons(5), Money.wons(10), Duration.ofSeconds(10)));
```
이렇게 코드를 작성함으로써 일반 요금정책과 심야 할인 요금정책을 테스트 할 수 있습니다. 

#### 부가 정책 적용하기
부가 정책은 2가지 제약조건에 따라 구현을 합니다. 
1. 부가 정책은 기본 정책이나 다른 부가 정책의 인스턴스를 참조할 수 있어야 합니다. 
2. Phone 입장에서는 자신이 기본 정책의 인스턴스에게 메시지를 전송하고 있는지, 부가 정책의 인스턴스에게 메시지를 전송하고 있는지를 몰라야합니다. 즉 기본 정책과 부가 정책은 협력안에서 동일한 **역할**을 수행해야 합니다.

이 내용에 따라 부가 정책을 클래스로 만들어보겠습니다.
```java
public abstract class AdditionalRatePolicy implements RatePolicy{
	private RatePolicy next;

	public AdditionalRatePolicy(RatePolicy next){
		this.next = next;
	}

	@Override
	public Money calculateFee(Phone phone){
		Money fee = next.calculateFee(phone);
		return afterCalculated(fee);
	}

	abstract protected Money afterCalculated(Money fee);
}
```
Phone의 입장에서 AdditionalRatePolicy는 RatePolicy의 역할을 수행하기에 RatePolicy 인터페이스를 구현합니다. 
또한 다른 요금 정책과 조합 될 수 있도록 RatePolicy 타입의 next라는 이름을 가진 인스턴스 변수를 내부에 포함합니다. 

그리고 이런 AdditionalRatePolicy를 자식 클래스들이 상속받아 구현하면 다음과 같습니다.
```java
public class TaxablePolicy extends AdditionalRatePolicy{
	private double taxRatio;

	public TaxablePolicy(double taxRation, RatePolicy next){
		super(next);
		this.taxRatio = taxRatio;
	}

	@Override
	protected Money afterCalculated(Money fee){
		return fee.plus(fee.times(taxRatio));
	}
}

public class RateDiscountPolicy extends AdditionalRatePolicy{
	private double taxRatio;

	public TaxablePolicy(Money discountAmount, RatePolicy next){
		super(next);
		this.discountAmount = discountAmount;
	}

	@Override
	protected Money afterCalculated(Money fee){
		return fee.minus(discountAmount);
	}
}
```

#### 기본 정책과 부가 정책 합성하기
##### 일반 요금제 + 세금 정책 조합
```java
Phone phone = new Phone(new TaxablePolicy(0.05, new RegularPolicy(...)));
```
##### 일반 요금제 + 기본 요금할인정책 + 세금 정책
```java
Phone phone = new Phone(new TaxablePolicy(0.05, new RateDiscountPolicy(Money.wons(1000), new RegularPolicy(...))));
```
...
처음엔 조합할 코드를 이해하기 어려울 수 있으나 설계에 익숙해지고 나면 상속을 이용한 방식보다 더 예측 가능하고 일관성 있다는 것을 알 수 있습니다.

### 믹스인
믹스인은 객체를 생성할 때 코드의 일부를 클래스안에 섞어 재사용하는 기법을 말합니다. 
합성이 실행 시점에 객체를 조합하는 재사용 방법이라면 믹스인은 컴파일 시점에 필요한 코드 조각을 조합하는 재사용 방법입니다. 

# 읽고 느낀 점
#### 합성이란?
책을 계속 읽으며 나오고 있는 **합성**이라는 단어.. 이것의 정체는 무엇인가..
처음에도 **합성**이라는 말이 나왔는데 뭔가 런타임 시점에 의존성을 변경할 수 있도록 도움을 준다라고 생각을 했습니다. 

흠.. 그렇게 알았지만..? 뤼튼을 통해 **합성**을 알아본 바로는 **클래스안에서 다른 클래스의 인스턴스를 관리하는 기법**이란 말을 합니다. 
그리고 다른 클래스의 인스턴스를 관리하는 방법들은 의존성을 관리를 통해 사용할 수 있습니다. 
#### 의존성 관리 방법 3가지
의존성 관리 방법 3가지는 생성자 주입, Setter Method 주입, 필드 주입이 있습니다.
책에선 합성의 의존성을 관리하는 방법중 Setter Method 주입을 많이 사용하던뎅 .. 흠 제가 생각했을 땐 Setter Method 주입의 최대 단점은 런타임 시점에 의존성을 관리한다는 것에 있습니다.

런타임 시점에 의존성을 관리하는 방법이 동적으로 원하는 객체를 넣을 수도 있지만 시간이 지나 문서화가 제대로 되어 있지 않으면 그만큼 찾기도 어려운 것이라 생각했습니다.
(단순히 제 생각..ㅎㅎ)

무튼 이런 특징으로 인해 전 최대한 생성자 주입을 사용하는 것이 좋다고 봅니다.
#### 생성자 주입
생성자 주입은 컴파일 시점에 어떤 인스턴스를 받을지 결정을 하기에 어떤 위치에서 의존성을 관리하는지 명확히 알 수 있습니다. 
그래서 제 생각엔 관리라는 측면에서 생성자 주입이 더 안전하고 좋은 방법이라 생각합니다.

뭐 .. 그래도 무조건 생성자 주입이 옳다는 아닙니다.
상황에 따라 어떻게 의존성을 관리할 수 있을 지는 바뀔 수 있다고 생각합니다. 