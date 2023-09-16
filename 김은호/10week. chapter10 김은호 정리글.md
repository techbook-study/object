# 간략한 내용 정리
객체지향에서는 코드를 재사용하기 위해 **새로운 코드**를 추가합니다. 
객체지향에서 코드는 일반적으로 클래스 안에 작성되기에 객체지향에서 클래스를 재사용하는 전통적인 방법은 새로운 클래스를 추가하는 것입니다. 

이번장은 클래스를 재사용하기 위한 대표적인 기법인 **상속**과 새로운 클래스의 인스턴스 안에 기존 클래스의 인스턴스를 포함시키는 방법인 **합성**에 대해 알아보고자 합니다. 

코드를 재사용하는 이유는 중복된 코드를 제거하기 위함입니다.
따라서 상속에 대해 살펴보기 전 중복된 코드의 문제점을 먼저 살펴보고자 합니다. 

### 상속과 중복코드
#### DRY 원칙
프로그램의 본질은 비즈니스와 관련된 지식을 코드로 변환하는 것에 있습니다. 
그리 고 이유가 무엇이건 새로운 코드를 추가하고 나면 언젠가는 변경될 것이란 생각을 해야합니다. 

문제는 변경되는 코드로 인해 중복코드가 생길 경우 모든 중복 코드에 대한 테스트가 필요하며, 모든 중복되는 코드의 결과는 같아야 할 것입니다.
그리고 변경사항에 따라 대해 중복되는 코드를 전부 변경해야 합니다.
이렇듯 중복 코드는 수정과 테스트에 드는 비용을 증가시키고 시스템과 개발자들을 공황 상태로 몰아넣을 수 있습니다.

그래서 신뢰할 수 있고 수정하기 쉬운 소프트웨어를 만들 수 있어야 하는데 그 효과적인 방법 중 하나는 중복을 제거하는 것입니다. 
앤드류 헌트와 데이비드 토마스의 말을 인용하자면 프로그래머들은 DRY원칙을 따라야 한다고 합니다. 

DRY는 반복하지 말라는 뜻의 Don't Repeat Yourself의 첫글자를 모아 만든 용어로 동일한 지식을 중복하지 말라는 의미를 가집니다. 

#### 중복과 변경
##### 중복 코드 살펴보기
중복 코드의 문제점을 이해하기 위해 한달에 한번씩 가입자 별로 전화 요금을 계산하는 간단한 애플리케이션을 만들어 본 것입니다. 
전화 요금을 계산하는 규칙은 간단한데, 단위 시간당 요금으로 나눠 주면 됩니다. 
10초당 5원의 통화료를 부고하는 요금제에 가입돼있는 가입자가 100초동안 통화를 했다면 요금으로 100/10 X 5 = 50원으로 부과됩니다. 

```java
public class Call{
	private LocalDateTime from;
	private LocalDateTime to;

	public Call(LocalDateTime from, LocalDateTime to){
		this.from = from;
		this.to = to;
	}

	public Duration getDuration(){
		return Duration.between(from, to);
	}

	public LocalDateTime getForm(){
		return from;
	}
}

public class Phone{
	private Money amount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public Phone(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}

	public void call(Call call){
		calls.add(call);
	}

	public List<Call> getCalls(){
		return calls;
	}

	public Money getAmount(){
		return amount;
	}

	public Duration getSeconds(){
		return seconds;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		for(Call call : calls){
			result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
		}
		return result;
	}
}

public class CallTest{
	
	@Test
	void 요금계산(){
		Phone phone = new Phone(Money.wons(5), Duration.ofSeconds(10));
		phone.call(new Call(LocalDateTime.of(2018, 1, 1, 12, 10, 0)),
						    LocalDateTime.of(2018, 1, 1, 12, 11, 0)));
		phone.call(new Call(LocalDateTime.of(2018, 1, 2, 12, 10, 0)),
						    LocalDateTime.of(2018, 1, 2, 12, 11, 0)));
		
		phone.calculateFee(); //=> Money.wons(60);
	}
}
```
해당 코드를 살펴보면 Call 클래스는 통화 시작 시간인 from, 통화 종료 시간인 to를 인스턴스 변수로 포함하고 있습니다. 

Phone클래스는 통화 요금을 계산하는 정보 전문가 클래스로 Call의 목록을 관리합니다. 
Phone 인스턴스는 요금 계산을 위해 단위 요금을 저장하는 amount, 단위 시간을 저장하는 seconds, Call의 전체 통화 목록을 가지는 Call의 리스트인 calls입니다. 

calculateFee 메서드는 amount, seconds, calls를 이용해 전체 통화 요금을 계산합니다. 

마지막으로 CallTest클래스에선 1분동안 두번 통화를 한 경우의 통화 요금을 계산하는 방법을 코드로 나타냈습니다. 

이 애플리케이션이 시간이 흘러 요구사항이 변경되었다고 가정해보겠습니다. 
변경된 요구 사항은 심야 요금 할인 요금제라는 새로운 요금 방식을 추가해야합니다.
심야 할인 요금제는 밤 10시 이후의 통화에 대해 요금을 할인 해주는 방식입니다. 

이 요구사항을 해결 하기 위해 가장 빠르게 Phone의 코드를 복사해 NightlyDiscountPhone이라는 새로운 클래스를 만든 후 수정했습니다. 

```java
public class NightlyDiscountPhone{
	private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;
	private Money regularAmount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds){
		this.nightlyAmount = nightlyAmount;
		this.regularAmount = regularAmount;
		this.seconds = seconds;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
				result = result.plus(nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			} else {
				result = result.plus(regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			}
		}
		
		return result;
	}
}
```
기존의 Phone클래스를 복사해 비교적 쉽게 코드를 구성했습니다. 
하지만 코드를 보면 NightlyDiscountPhone과  Phone 클래스는 calculateFee 메서드외에도 여러가지 부분에서 중복된 코드가 발생하게 됩니다. 

이러한 중복된 코드는 언제 터질지 모르는 시한폭탄과도 같습니다. 
그래서 언젠가 코드를 변경해야 할 때 폭탄의 뇌관이 당겨질지, 아니면 아무일 없었다는 듯이 평화롭게 지나갈지는 그 누구도 알지 못하게 됩니다.

##### 중복 코드 수정하기
중복코드가 코드 수정에 미치는 영향을 확인하기 위해 새로운 요구사항을 추가해보도록 하겠습니다. 
이번에 추가할 기능은 통화 요금에 부과할 세금을 계산하는 것입니다. 

부과되는 세율이 가입자의 핸드폰마다 다르다고 가정하겠습니다.
현재 통화 요금 계산 로직은 Phone과 NightlyDiscountPhone 양쪽 모두에 구현돼있기에 세금을 추가하기위해선 두 클래스를 함께 수정해야 합니다.
```java
public class Phone{
	...
	private double taxRate;

	public Phone(Money amount, Duration seconds, double taxRate){
		...
		this.taxRate = taxRate;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		for(Call call : calls){
			result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
		}
		
		return result.plus(result.times(taxRate));
	}
}

public class NightlyDiscountPhone{
	...
	private double taxRate;

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate){
		...
		this.taxRate = taxRate;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
				result = result.plus(nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			} else {
				result = result.plus(regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			}
		}
		
		return result.minus(result.times(taxRate));
	}
}
```
해당 예제는 중복코드가 가지는 단점을 잘 보여주고 있습니다. 
많은 코드 더미 속에서 어떤 코드가 중복인지를 파악하는 일은 쉬운 일이 아닙니다. 
중복 코드는 항상 함께 수정되어야 하기에 수정할 때 하나라도 빠드린다면 버그로 이어집니다. 
예를 들어, Phone 클래스는 수정했지만 NightlyDiscountPhone을 수정하지 않았다면 심야 할인 요금제는 모든 가입자에게 세금이 부과되지 않는 장애가 발생할 것입니다. 

결국 중복코드는 새로운 중복 코드를 만들게 됩니다. 
중복코드를 제거하지 않은 상태에서 코드를 수정할 수 있는 유일한 방법은 새로운 중복 코드를 추가하는 것 뿐입니다. 

위의 문제로 인해 중복 코드를 추가하는 과정에서 코드의 일관성이 무너지는 위험이 생기고, 이로 인해 중복 코드가 많아 질수록 버그의 수는 증가하고 그에 비례해 코드를 변경하는 속도는 점점 더 느려집니다. 
##### 타입 코드 사용하기
두 클래스 사이의 중복 코드를 제거하는 한가지 방법은 클래스를 하나로 합치는 것입니다. 
다음의 코드와 같이 요금제를 구분하는 타입 코드를 추가하고 타입 코드의 값에 따라 로직을 분리시켜 Phone과 NightlyDiscountPhone을 하나로 합칠 수 있습니다. 
하지만 계속 강조했던 것 처럼 타입 코드를 사용하는 클래스는 낮은 응집도와 높은 결합도라는 문제에 시달리게 됩니다. 

```java
public class Phone{
	private static final int LATE_NIGHT_HOUR = 22;
	enum PhoneType {REGULAR, NIGHTLY};

	private PhoneType type;
	
	private Money amount;
	private Money nightlyAmount;
	private Money regularAmount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public Phone(PhoneType type, Money amount, Money nightlyAmount, 
				 Money regularAmount, Duration seconds){
		this.type = type;
		this.amount = amount;
		this.regularAmount = regularAmount;
		this.nightlyAmount = nightlyAmount;
		this.seconds = seconds;
	}

	public Phone(Money amount, Duration seconds){
		this(PhoneType.REGULAR, amount, Money.ZERO, Money.ZERO, seconds);
	}

	public Phone(Money nightlyAmount, Money regularAmount, Duration seconds){
		this(PhoneType.NIGHTLY, Money.ZERO, nightlyAmount, regularAmount, seconds);
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		for(Call call : calls){
			if(type == PhoneType.REGULAR){
				result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			}else{
				if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
					result = result.plus(nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
				}else{
					result = result.plus(regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
				}
			}
		}
		return result;
	}
}
```
객체지향 프로그래밍 언어는 타입 코드를 사용하지 않고도 중복 코드를 관리할 수 있는데 그 방법이 상속입니다.
#### 상속을 이용해 중복 코드 제거하기
상속은 이미 존재하는 클래스와 유사한 클래스가 필요하다면 코드를 복사하지 않고 상속을 이용해 코드를 재사용하면 됩니다. 
예를 들어 앞서 본 NightlyDiscountPhone 클래스는 Phone 클래스와 코드가 거의 유사합니다. 

```java
public class NightlyDiscountPhone extends Phone{
	private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds){
		super(regularAmount, seconds);
		this.nightlyAmount = nightlyAmount;
	}

	@Override
	public Money calculateFee(){
		// 부모 클래스의 calculateFee 호출
		Money result = super.calculateFee();
		
		Money nightlyFee = Money.ZERO;
		for(Call call : getCalls()){
			if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
				nightlyFee = nightlyFee.plus(
					getAmount().minus(nightlyAmount).times(
						call.getDuration().getSeconds() / getSecondes().getSecondes()
					)
				);
			}
		}
		
		return result.minus(nightlyFee);
	}
}
```
해당 코드는 Phone 클래스의 기능을 최대한 상속받아 재정의해 사용하는 것을 목표로 했습니다. 
기본적으로는 10시 이전엔 Phone 클래스를 사용해 요금을 계산하도록 했고, 10시 이후엔  NightlyDiscountPhone 클래스에서 사용하기로 했습니다. 

그런데 10시 이전과 10시 이후에 적용되는 요금 계산이 서로 다르다 보니 NightlyDiscountPhone 클래스에서 그 값을 계산하는 로직을 구현할 필요가 있었습니다. 

그래서 결론적으로는 calculateFee() 메서드를 재정의하여 요금을 계산할 때 먼저는 부모 클래스인 Phone 클래스에서 시간이 상관없이 기본 요금값을 가져옵니다. 
10시 이후 로직은 (10시 이전 전화 요금 - 10시 이후 전화 요금)을 구한뒤 10시 이후 통화 시간의 값을 곱합니다.
그 뒤 모든 기본 요금값 - 10시 이후 요금 계산 로직값을 통해 10시 이전 값과 10시 이후 값에 해당하는 값을 구합니다. 

해당 로직은 생각외로 복잡합니다. 왜냐하면 기대했던 로직은 10시 이전의 요금에서 10시 이후의 로직을 빼는 것이 아니라 10시 이전의 로직과 10시 이후의 로직을 더하는 것을 원했기 때문입니다. (아무래도 더하는 것이 좀 더 직관적이기 때문에)
이렇듯 요구 사항과 구현 사이의 차이가 클수록 코드를 이해하기 어려워 지고 잘못된 상속은 이 차이를 더 크게 벌리게 만듭니다. 

#### 강하게 결합된 Phone과 NightlyDiscountPhone
만약 위의 코드에서 세금을 부과하는 요구사항이 추가한다면 다음과 같을 것입니다.

```java
public class Phone{
	...
	private double taxRate;

	public Phone(Money amount, Duration seconds, double taxRate){
		...
		this.taxRate = taxRate;
	}

	public Money calculateFee(){
		...
		return result.plus(result.times(taxRate));
	}

	public double getTaxRate(){
		return taxRate;
	}
}

public class NightlyDiscountPhone{
	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate){
		super(regularAmount, seconds, taxRate);
		...
	}

	@Override
	public Money calculateFee(){
		...
		return result.minus(nightlyFee.times(getTaxRate()));
	}
}
```
해당 코드를 통해 확인할 수 있었던 것은 상속을 통해 코드의 재사용을 목적으로 했지만 결국은 세금 적용이라는 새로운 요청에서 중복된 수정이 일어난다는 것입니다. 

결론은 상속을 사용하면 적은 노력으로도 새로운 기능을 쉽고 빠르게 추가할 수 있지만 그로 인해 중복된 코드를 추가하는 대가를 치러야 할수도 있습니다. 

이처럼 상속관계로 인해 연결된 자식 클래스가 부모 클래스의 변경에 취약해지는 현상을 가르켜 기반 클래스 문제라고 부릅니다. 

### 취약한 기반 클래스 문제
부모 클래스의 변경에 의해 자식 클래스가 영향을 받는 현상을 취약한 기반 클래스 문제라고 부릅니다. 

취약한 기반 클래스 문제는 캡슐화를 약화시키고 결합도를 높이게 만듭니다. 
상속은 자식 클래스가 부모 클래스의 구현 세부사항에 의존하도록 만들기에 캡슐화를 약화시킵니다. 
이것은 상속이 위험한 이유인 동시에 우리가 상속을 피해야 하는 첫번째 이유입니다. 

객체를 사용하는 이유는 구현과 관련된 세부사항을 퍼블릭 인터페이스 뒤로 캡슐화할 수 있기때문인데, 안타깝게도 상속을 사용하면 부모 클래스의 퍼블릭 인터페이스가 아닌 구현을 변경하더라도 자식 클래스가 영향을 받기 쉬워지게 됩니다. 

객체 지향의 기반은 캡슐화를 통한 변경의 통제이나 상속은 캡슐화의 장점을 희석시키고, 구현에 대한 결합도를 높이게되 객체 지향이 가진 강력함을 반감 시킵니다. 

#### 불필요한 인터페이스 상속 문제
자바 초기 버전에 상속을 잘못 사용한 대표적인 사례는 java.util.Properties와 java.util.Stack입니다. 

먼저 Stack은 Vector를 상속받아 구현됩니다. 
이때 Vector는 임의의 위치에서 index 요소를 조회, 추가, 삭제할 수있는 get, add, remove 오퍼레이션을 제공합니다. 
반면 Stack은 맨 마지막 위치에서만 요소를 추가하거나 제거할 수 있는 push, pop 오퍼레이션을 제공합니다.

문제는 Stack이 Vector를 상속받았기에 모든 Vector의 메서드를 사용할 수 있게 되는 문제가 생깁니다. 
해당 문제는 Stack의 LIFO에 목적과는 맞지 않는 작업을 진행하게 됨으로 문제가 될 소지가 있습니다. 

```java
Stack<String> stack = new Stack<>();
stack.push("1st");
stack.push("2nd");
stack.push("3rd");
stack.add(0, "4th");

assertEquals("4th", stack.pop()); // 에러 발생
```
위의 코드를 보면 당연히 assert 값이 4th가 나올 것 같지만 add Vector의 메서드임으로 assert 값은 "3rd"를 사용해야 합니다. 

다음은 java.util.Properties 클래스입니다. 
Properties 클래스는 HashTable 클래스를 상속받아 구현되었으며, Map과 유사하지만 다양한 타입을 저장할 수 있는 Map과 달리 키와 값의 타입으로 오로지 String만 저장할 수 있습니다. 

```java
Properties properties = new Properties();
properties.setProperty("A","C++");
properties.setProperty("B","JAVA");
properties.put("Kim", 67);

assertEquals("C", properties.getProperty("Kim")); // 에러 발생
```
이 코드도 Stack과 마찬가지로 부모 클래스의 메서드를 사용해 assert해봅니다. 
하지만 결과값 확인을 위해 사용한 getProperty 메서드에선 null이라는 값을 가져옵니다. 
또한 더 문제는 put 메서드를 사용하는 시점에서 value값이 String이 아닌데도 값을 입력하는 문제를 가집니다. 

이렇듯 퍼블릭 인터페이스에 대한 고려없이 단순히 코드를 재사용하기 위해 상속을 이용하는 것은 상당히 위험한 것임을 알 수 있습니다. 
`상속받은 부모 클래스의 메서드가 자식 클래스의 내부 구조에 대한 규칙을 깨트릴 수 있다.`
#### 메서드 오버라이딩의 오작용 문제
조슈아 블로치는 이펙티브 자바에서 HashSet의 구현에 강하게 결합된 InstrumentedHashSet 클래스를 소개합니다. 

```java
public class InstrumentedHashSet<E> extends HashSet<E>{
	private int addCount = 0;

	@Override
	public boolean add(E e){
		addCount++;
		return super.add(e);
	}

	@Override
	public boolean addAll(Collection<? extends E> c){
		addCount += c.size();
		return super.addAll(c);
	}
}

public class TestData{
	@Test
	void test(){
		InstrumentedHashSet<String> languages = new InstrumentedHashSet<>();
		languages.addAll(Arrays.asList("Java", "Ruby", "Scala"));
	}
}
```
대부분의 사람들은 위의 코드가 실행한 후 addCount의 값이 3이 될 것이라 예상합니다. 
하지만 실제 실행한 이후의 addCount값은 6이 됩니다. 
그 이유는 부모 클래스인 HashSet의 addAll 메서드 안에서 add 메서드를 호출하기 때문입니다.

먼저 InstrumentedHashSet의 addAll 메서드가 호출돼서 addCount에 3이 더해집니다. 
그 후 super.addAll 메서드가 호출되고 제어는 부모 클래스인 HashSet으로 이동합니다. 
불행하게도 HashSet은 각각의 요소를 추가하기 위해 내부적으로 add 메서드를 호출하고 결과적으로 InstrumentedHashSet의 add 메서드가 세번 호출되어 addCount에 3이 더해지게 됩니다. 

위의 문제를 해결하는 방법은 InstrumentedHashSet의 addAll 메서드를 제거하는 것입니다. 
그렇게 되면 HashSet의 addAll이 InstrumentedHashSet의 add만 실행하기 때문입니다. 
하지만 나중에 HashSet의 addAll 메서드가 add 메세지를 전송하지 않도록 수정한다면 addAll 메서드를 이용한 추가 요소들은 카운터가 누락될 것입니다.

그래서 이 문제의 해결을 위해 다음과 같이 코드를 수정할 수 있습니다.
```java
public class InstrumentedHashSet<E> extends HashSet<E>{
	private int addCount = 0;

	@Override
	public boolean add(E e){
		addCount++;
		return super.add(e);
	}

	@Override
	public boolean addAll(Collection<? extends E> c){
		boolean modified = false;
		for(E e:c){
			if(add(e)){
				modified = true;
			}
		}
		return modified;
	}
}
```
하지만 위와 같이 코드를 수정하는 것 또한 문제가 발생할  수 있는데 그것은 오버라이딩된 addAll메서드의 구현이 HashSet과 동일하다는 것입니다. 
즉 미래에 발생할지도 모르는 위험을 방지하기 위해 코드를 중복시킨 것입니다. 

거기다 부모 클래스를 매번 그대로 들고 오는 것은 항상 가능하지 않을 수도 있습니다. 
왜냐하면 코드에 대한 접근 권한이 없거나 private 변수나 메서드를 사용하고 있을 수 있기 때문입니다.

그래서 조슈아 블로치는 클래스가 상속되기를 원한다면 상속을 위해 클래스를 설계하고 문서화해야한다고 합니다. 
하지만 객체지향의 핵심이 구현을 캡슐화 하는 것인데 이렇게 내부구현을 하는 것은 좋지 않은 방법이 될 수도 있습니다. 

그렇기에 이러한 기법을 사용함에 있어선 언제나 설계라는 관점에서 적절한 트레이드 오프를 할 수 있어야 합니다. 

#### 부모 클래스와 자식 클래스의 동시 수정 문제
음악 목록을 추가할 수 있는 플레이리스트를 구현한다고 가정해보겠습니다. 
필요한 것은 음악 정보를 저장할 Song 클래스와 음악 목록을 저장할 Playlist 클래스 입니다. 

```java
public class Song{
	private String singer;
	private String title;

	public Song(String singer, string title){
		this.singer = singer;
		this.title = title;
	}

	public String getSinger(){
		return singer;
	}

	public String getTitle(){
		return title;
	}
}

public class Playlist{
	private List<Song> tracks = new ArrayList<>();

	public void append(Song song){
		getTracks().add(song);
	}

	public List<Song> getTracks(){
		return tracks;
	}
}

public class PersonalPlaylist extends Playlist{
	public void remove(Song song){
		getTracks().remove(song);
	}
}
```
Playlist는 트랙에 노래를 추가할 수 있는 append 메서드를 추가합니다.
그리고 플레이리스트에서 노래를 삭제할 수 있는 기능이 추가된 PersonalPlaylist 클래스가 있습니다. 
PersonalPlaylist의 구현을 가장 쉽게 하는 방법은 Playlist를 상속받는 것입니다. 

하지만 요구사항이 변경되면서부터 문제가 발생하는데 Playlist에 노래 목록뿐 아니라 가수별 노래의 제목도 함께 관리된다고 가정해보겠습니다. 

```java
public class Playlist{
	private List<Song> tracks = new ArrayList<>();
	private Map<String, String> singers = new HashMap<>();

	public void append(Song song){
		tracks.add(song);
		singers.put(song.getSinger(), song.getTitle());
	}

	public List<Song> getTracks(){
		return tracks;
	}

	public Map<String, String> getSingers(){
		return singers;
	}
}

public class PersonalPlaylist extends Playlist{
	public void remove(Song song){
		getTracks().remove(song);
		getSingers().remove(song.getSinger());
	}
}
```
이번 코드에선 Playlist(부모 클래스)에 가수별 노래의 제목을 추가했을 뿐인데 PersonalPlaylist(자식 클래스)에서도 수정이 발생했다는 것을 보여주는 코드입니다. 
이러한 문제가 발생하는 것은 상속을 사용하게되어 자식 클래스가 부모 클래스의 구현에 강하게 결합이 되어 있기 때문입니다. 

결합도란 다른 대상에 대해 알고 있는 지식의 양인데, 상속은 기본적으로 부모 클래스의 구현을 재사용한다는 기본 전제를 따르고 있어 부모 클래스의 내부를 속속들이 알도록 강요합니다. 
따라서 코드 재사용을 위해 상속은 부모 클래스와 자식 클래스를 강하게 결합시키기에 수정해야 하는 상황 역시 빈번하게 일어나게 됩니다. 
`클래스를 상속하면 결합도로 인해 자식 클래스와 부모 클래스의 구현을 영원히 변경하지 않거나, 자식 클래스와 부모 클래스를 동시에 변경하거나 둘 중 하나를 선택해야 합니다.`

### Phone 다시 살펴보기
이제부턴 Phone과 NightlyDiscountPhone의 상속으로 인한 문제를 최소화 할 수 있는 방법을 찾아보겠습니다.
#### 추상화에 의존하자
NightlyDiscountPhone의 가장 큰 문제점은 Phone에 강하게 결합되어 있어 Phone이 변경될 경우 같이 변경되는 문제를 가지고 있습니다. 
이 문제를 해결하는 가장 일반적인 방법은 자식 클래스와 부모 클래스 모두 추상화에 의존하도록 하는 것입니다.
#### 차이를 메서드로 추출하라
문제를 해결하기 위해 가장 먼저 할 일은 중복 코드 안에서 차이점을 별도의 메서드로 추출하는 것입니다. 
이것은 흔히 말하는 변하는 것으로 부터 변하지 않는 것을 분리하라 또는 변하는 부분을 찾고 이를 캡슐화하라 라는 조언을 메서드에서 적용한 것입니다.

먼저 위에서 봤던 중복된 코드를 보겠습니다.
```java
public class Phone{
	private Money amount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public Phone(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}


	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result = result.plus(amount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
		}
		
		return result;
	}
}

public class NightlyDiscountPhone{
	private static final int LATE_NIGHT_HOUR = 22;

	private Money nightlyAmount;
	private Money regularAmount;
	private Duration seconds;
	private List<Call> calls = new ArrayList<>();

	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds){
		this.nightlyAmount = nightlyAmount;
		this.regularAmount = regularAmount;
		this.seconds = seconds;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
				result = result.plus(nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			} else {
				result = result.plus(regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds()));
			}
		}
		
		return result;
	}
}
```
먼저 할일은 두 클래스의 메서드에서 다른 부분을 별도의 메서드로 추출합니다. 
이 경우 calculateFee의 for문 안에 구현된 요금 게산 로직이 서로 다르다는 사실을 알 수 있습니다.
그리고 이러한 다른 부분을 코드로 적용하면 다음과 같습니다. 

```java
public class Phone{
	...
	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result = result.plus(calculateCallFee(call));
		}
		
		return result;
	}

	private Money calculateCallFee(Call call){
		return amount.times(call.getDuration().getSeconds()/seconds.getSeconds());
	}
}

public class NightlyDiscountPhone{
	...

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result = result.plus(calculateCallFee(call));
		}
		
		return result;
	}

	private Money calculateCallFee(Call call){
		if(call.getForm().getHour() >= LATE_NIGHT_HOUR){
			return nightlyAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		} else{
			return regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		}
	}
}
```
Phone, NightlyDiscountPhone 클래스를 보면 각각 calculateFee() 메서드안에서 중복되는 내용을 제거했음을 확인할 수 있습니다. 
그리고 변화가 생긴 부분을 따로 calculateCallFee(Call call) 메서드 형태로 만들어 각각 필요한 구현을 진행했습니다. 
#### 중복 코드를 부모 클래스로 올려라
목표는 모든 클래스들이 추상화에 의존하도록 만드는 것이기에 새로이 부모 클래스를 추가해보겠습니다. 
새로운 부모 클래스의 이름은 AbstractPhone으로 하나고 Phone과 NightlyDiscountPhone이 AbstractPhone을 상속 받을 것입니다. 

```java
public abstract class AbstractPhone{
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

public class Phone extends AbstractPhone{
	private Money amount;
	private Duration seconds;
	
	public Phone(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}

	@Override
	protected Money calculateCallFee(Call call){
		return amount.times(call.getDuration().getSeconds()/seconds.getSeconds());
	}
}

public class NightlyDiscountPhone extends AbstractPhone{
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
		} else{
			return regularAmount.times(call.getDuration().getSeconds()/seconds.getSeconds());
		}
	}
}
```
코드를 보면 AbstractPhone(부모 클래스)에는 Phone과 NightlyDiscountPhone에서 공통적으로 적용되던 메서드들을 모아두었습니다. 

그리고 Phone과 NightlyDiscountPhone에서 Money calculateCallFee(Call call)는 이름은 같지만 구현이 달리 되는 부분이었기에 각 자식 클래스에서 구현할 수 있도록 추상 메서드로 만들었습니다.

각 자식 클래스는 추상 메서드인 Money calculateCallFee를 상속받아 각 클래스의 기능에 맞게 구현하였습니다. 
이렇게 함으로써 Phone과 NightlyDiscountPhone(자식 클래스)는 AbstractPhone(부모 클래스)를 향하여 추상화에 의존하게 되었고 이로 인해 유연한 애플리케이션을 만들 수 있게 되었습니다. 

#### 추상화가 핵심이다
공통 코드를 이동시킨 후에 각 클래스는 서로 다른 변경의 이유를 가집니다. 
AbstractPhone은 전체 통화목록을 계산하는 방법이 바뀔 경우에만 변경이 됩니다. 
Phone은 일반 요금제의 통화 한건을 계산하는 방식이 바뀔 경우에만 변경됩니다. 
NightlyDiscountPhone은 심야 할인 요금제의 통화 한건을 계산하는 방식이 바뀔 경우에만 변경됩니다. 
이처럼 세 클래스는 각각 하나의 변경 이유만을 가집니다. 

또한 새로운 요금 정책을 추가하더라도 다른 코드엔 영향을 미치지 않습니다. 
왜냐하면 추상화된 AbstractPhone의 calculateCallFee를 상속받아 구현을 하면 되기 때문입니다. 
그렇기에 현재의 설계는 확장에는 열려있고 수정에는 닫혀 있는 개방-폐쇄 원칙을 준수합니다. 

지금까지 살펴본 모든 장점은 클래스들이 추상화에 의존하기에 얻어지는 장점입니다. 
상속 계층이 코드를 진화시키는데 걸림돌이 된다면 추상화를 찾아내고 상속 계층 안의 클래스들이 그 추상화에 의존하도록 코드를 리팩터링하면 됩니다. 

#### 의도가 드러내는 이름 선택하기
여지것 클래스가 추상화에 의존하도록 만들었지만 여전히 아쉬운 것은 클래스의 이름과 관련된 것입니다. 
NightlyDiscountPhone은 심야 할인 요금제를 구현한다는 것이 와닿지만 AbstractPhone, Phone은 일반 요금제와 관련된 내용을 구현한다는 사실을 명시적으로 전달하지 못합니다. 

게다가 NightlyDiscountPhone과 Phone은 사용자가 가입한 전화기의 한 종류이지만 AbstractPhone이라는 이름은 전화기를 포괄하기에 의미를 명확히 전달하지 못합니다. 
그렇기에 AbstractPhone을 Phone으로 Phone을 RegularPhone으로 변경하는 것이 적절해 보입니다. 

```java
public abstract class Phone{ ... }

public class RegularPhone extends Phone{ ... }

public class NightlyDiscountPhone extends Phone{ ... }

```
#### 세금 추가하기
```java
public abstract class Phone{
	private double taxRate;
	private List<Call> calls = new ArrayList<>();

	public Phone(double taxRate){
		this.taxRate = taxRate;
	}

	public Money calculateFee(){
		Money result = Money.ZERO;
		
		for(Call call : calls){
			result = result.plus(calculateCallFee(call));
		}
		
		return result.plus(result.times(taxRate));
	}

	abstract protected Money calculateCallFee(Call call);
}

public class RegularPhone extends Phone{
	... 
	public RegularPhone(Money amount, Duration seconds, double taxRate){
		super(taxRate);
		this.amount = amount;
		this.seconds = seconds;
	}
	...
}

public class NightlyDiscountPhone extends Phone{
	... 
	public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds, double taxRate){
		super(taxRate);
		this.nightlyAmount = nightlyAmount;
		this.regularAmount = regularAmount;
		this.seconds = seconds;
	}
}
```
만약 세금을 추가하게 된다면 코드들을 다음과 같이 추가할 수 있을 것입니다. 
그런데 현재 세금(taxRate)를 Phone(부모 클래스)에 추가하게 되면서 RegularPhone, NightlyDiscountPhone(자식 클래스)도 소스에 변경이 생깁니다. 

결국 상속에 의한 프로그래밍은 어쩔 수 없이 부모와 자식간의 코드를 수정할 수 밖에 없는 경우가 발생합니다. 
메서드 구현에 대한 결합은 추상 메서드를 추가함으로 어느 정도 완화할 수 있었지만 인스턴스 변수에 대한 잠재적인 결합을 제거할 수 있는 방법은 없습니다. 

### 차이에 의한 프로그래밍
기존 코드와 다른 부분만 추가함으로써 애플리케이션의 기능을 확장하는 방법을 차이에 의한 프로그래밍이라고 부릅니다. 
차이에 의한 프로그래밍의 목표는 중복 코드를 제거하고 코드를 재사용하는 것입니다. 

코드를 재사용하는 것은 단순히 문자를 타이핑하는 수고를 덜어주는 수준의 문제가 아닙니다. 
재사용 가능한 코드란 심각한 버그가 존재하지 않는 코드입니다. 
따라서 코드를 재사용하면 코드의 품질을 유지하면서도 코드를 작성하는 노력과 테스트는 줄일 수 있습니다. 

중복 코드를 제거하고 코드를 재사용하는 가장 쉬운 방법은 상속입니다. 
하지만 상속을 오용과 남용할 경우 애플리케이션을 이해하고 확장하기 어렵게 만듭니다. 
그렇기에 정말 필요한 경우에만 상속을 사용하는 것이 좋습니다. 
# 읽고 느낀 점
