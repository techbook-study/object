# 간략한 내용 정리
## 메시지와 인터페이스
훌륭한 객체 지향 코드를 얻기 위해선 협력안에서 객체가 수행하는 책임에 초점을 맞춰야합니다. 여기서 중요한 것은 책임이 객체가 수신할 수 있는 메시지 기반이 된다는 것입니다. 

그리고 객체가 수신하는 메시지들이 객체의 퍼블릭 인터페이스를 구성합니다. 여기서 훌륭한 퍼블릭 인터페이스를 만들기 위해선 책임 주도 설계를 따르는 것 이외에도 유연하고 재사용 가능한 퍼블릭 인터페이스를 만드는데 도움이 되는 설계 원칙과 기법을 익히고 적용해야합니다.

다음으로 이러한 원칙과 기법들을 살펴보고자 합니다. 

### 협력과 메시지 
#### 클라이언트-서버 모델
두 객체 사이의 협력관계를 설명하기 위해 사용하는 전통적인 메타포는 **클라이언트-서버**모델입니다. 협력안에서 메시지를 전송하는 객체를 클라이언트, 메시지를 전송하는 객체를 서버라고 부릅니다. 
이때 협력은 클라이언트가 서버의 서비스를 요청하는 단방향 상호작용이라 하는데 다음의 그림을 통해 설명하고자 합니다.

P176 그림 6.1
그림은 Screening과 Movie사이의 협력을 나타낸 것입니다. Screening은 클라이언트, Movie는 서버의 역할을 하고 있습니다. Screening은 **가격을 계산하라**는 메시지를, Movie는 **가격을 계산하는 서비스**를 메시지로 전달합니다.

P176 그림 6.2
다음은  Movie와 DiscountPolicy사이의 협력을 나타낸 것입니다. Screening은 클라이언트, Movie는 서버의 역할을 하고 있습니다. Movie는 최종 예매를 위해 **할인 요금을 계산**하라는 메시지를 수행해야 하지만 할인 요금을 계산하기 위한 정보가 부족하기에 DiscountPolicy에 필요한 정보를 받기 위한 메시지를 전송하고 DiscountPolicy는 그 정보를 메시지로 응답해줍니다.

이렇듯 클라이언트와 서버는 상황에 따라 달라질 수 있으며, 객체가 독립적으로 수행하는 것 보다 더 큰 책임을 수행하기 위해선 다른 객체와 협력이 필요하다는 것을 알 수 있습니다. 그리고 두 객체 사이의 협력을 가능하게 해주는 매개체가 바로 메시지입니다.

본격적인 설명에 앞서 객체지향 자료를 보며 혼란스러웠던 용어를 정리 후 본격적인 내용을 전달하고자 합니다. 

#### 메시지와 메시지 전송
**메시지**는 객체들이 협력하기 위해 사용할 수 있는 유일한 의사소통 수단입니다. 그리고 한 객체가 다른 객체에게 도움을 요청하는 것을 **메시지 전송** 또는 **메시지 패싱**이라고 부릅니다. 이때 메시지를 전송하는 객체를 **메시지 전송자**라고 부르며 메시지를 수신하는 객체를 **메시지 수신자**라고 부릅니다.

P178 그림 6.4
메시지는 **오퍼레이션명**과 **인자**로 구성되며 메시지 전송은 여기에 메시지 수신자를 추가한 것입니다. 따라서 메시지 전송은 메시지 수신자, 오퍼레이션명, 인자의 조합입니다. 

#### 메시지와 메서드
메시지를 수신했을 때 실제로 실행되는 함수 또는 프로시저를 메서드라고 합니다. 
메시지와 메서드의 구분은 메시지 전송자와 수신자가 느슨하게 결합될 수 있게 합니다. 이것이 가능한 이유는메시지 전송자는 어떤 메시지를 전송해야 하는지만 알면 되고, 메시지 수신자는 수신받은 메시지를 처리한 뒤 응답하기만 하면 되기 때문입니다. 

#### 퍼블릭 인터페이스와 오퍼레이션
외부의 객체는 오직 객체가 공개하는 메시지를 통해서만 객체와 상호작용할 수 있습니다. 그리고 객체가 의사소통하기위해 외부에 공개하는 메시지의 집합을 **퍼블릭 인터페이스**라고 합니다. 
또한 프로그래밍 언언의 관점에서 퍼블릭 인터페이스에 포함된 메시지를 **오퍼레이션**이라고 합니다. 

P180 그림6.5
UML에서는 공식적으로 오퍼레이션을 실행하기위해 객체가 호출될 수 있는 변환이나 정의에 관한 명세라고 정의합니다. 
즉 **오퍼레이션**을 **추상화**라고 표현하고 **오퍼레이션에 대한 구현**을 **메서드**라고 표현한것 입니다. 

#### 시그니처 
오퍼레이션(또는 메서드)의 이름과 파라미터의 목록을 합쳐 시그니처(signature)라고 부릅니다. 
하나의 오퍼레이션에 하나의 메서드만 존재하는 경우 꽤 단순하지만 다형성의 사용을 위해선 하나의 오퍼레이션에 다양한 메서드를 구현해야 합니다. 

### 인터페이스와 설계 품질
3장에서 좋은 인터페이스는 최소한의 인터페이스와 추상적인 인터페이스라는 조건을 만족해야한다고 합니다.
그리고 최소주의를 따르면서도 추상적인 인터페이스를 설계하는 가장 좋은 방법은 책임 주도 설계 방법을 따르는 것이고, 좋은 인터페이스를 얻기 위한 특징을 아는 것은 좋은 설계를 위한 방향을 제시할 것입니다. 

그래서 먼저 퍼블릭 인터페이스의 품질에 영향을 미치는 다음과 같은 원칙과 기법에 대한 내용을 정리해보고자 합니다. 

#### 디미터 법칙
P183 그림 6.6
그림의 ReservationAgency는 Screening, Movie, Reservation, DiscountCondition과 강하게 결합되어 있습니다. 
그렇기에 Screening, Movie, DiscountCondition의 변수나 메서드의 변경에도 ReservationAgency는 쉽게 변경되는 의존성의 집결지가 됩니다. 

이러한 의존성 높은 객체의 내부 구조에 대한 결합으로 발생하는 문제를 해결하기 위해 나온 것이 디미터의 법칙입니다. 
디미터 법칙은 **낯선자에게 말하지 말고, 오직 인접한 이웃에게만 말하라**는 법칙입니다. 
이렇게만 말하면 조금 개념에 대한 이해가 어려운 것 같은데 조금 더 풀어서 설명하면 **객체들의 협력 경로를 제한하면 결합도를 효율적으로 낮출 수 있다는 것**을 말합니다. 
그리고 객체들간의 메시지를 전송하는 인스턴스들은 **this 객체, 메서드의 매개변수, this의 속성, this의 속성인 컬렉션의 요소, 메서드 내에서 생성된 지역 객체**라고 할 수 있습니다. 

P185 그림6.7
그림은 이전 설계와는 다르게 ReservationAgency의 의존성이 Screening, Reservation과 생기게 됩니다. 

```java
public class ReservationAgency{
	public Reservation reserve(Screening screening, Customer customer, int audienceCount){
		Money fee = screening.calculateFee(audienceCount);
		return new Reservation(...);
	}
}
```
해당 코드는 위의 의존성 높은 ReservationAgency에서 의존성을 풀어낸 형태입니다. 이 코드에선 reserve메서드의 인자값인 screening을 이용해서만 객체들과 소통하게 됩니다. 
그리고 이렇게 코드를 수정함으로써 디미터의 법칙을 따른 결과에 대한 설계처럼 표현이 됩니다. 

```java
screening.getMovie().getDiscountConditions();
```
지금 보여준 코드는 디미터의 법칙을 어기고 있는데 이와 같은 코드 형태를 **기차 충돌**이라고 부르게 됩니다. 
기차 충돌이라고 부르는 이유는 **수신자의 내부 구조를 반환받은 요소에 대해 연쇄적으로 메시지를 전송**하기 때문입니다. 
이러한 구조는 메시지 전송자가 내부구조를 자세히 알게 되는 문제로 인해 수신자의 캡슐화는 무너지고, 메시지 전송자가 메시지 수신자와 강하게 결합되는 문제를 가져옵니다. 

#### 묻지 말고 시켜라
디미터 법칙은 훌륭한 메시지는 **객체의 상태에 관해 묻지 말고 원하는 것을 시켜야한다는 사실**을 강조합니다.
그리고 묻지 말고 시켜라는 이런 스타일의 메시지 작성을 장려하는 원칙을 가르키는 용어라고 보면 됩니다.
즉 묻지 말고 시켜라는 **송신자가 원하는 것을 시키는 메시지 작성 방식을 강조**함으로 밀접하게 연관된 정보와 행동을 함께 가지는 객체를 만들 수 있고, 이를 통해 응집도 높은 클래스를 얻을 수 있게 된다고 합니다.

#### 의도를 드러내는 인터페이스 
켄트 벡은 메서드를 명명하는 방법을 2가지로 설명하고 있습니다. 
메서드를 명명하는 방법 첫번째는 메서드가 작업을 어떻게 수행하는지를 나타내도록 이름을 짓는 것입니다. 그리고 이러한 방법으로 코드를 짜면 다음과 같이 만들 수 있습니다. 
```java 
public class PeriodCondition{
	public boolean isSatisfiedByPeriod(Screening screening);
}

public class SequenceCondition{
	public boolean isSatisfiedBySequence(Screening screening);
}
```
하지만 이렇게 코드를 만들게 되면 다음의 2가지 이유로 좋지 않은 부분이 있습니다. 
좋지 않은 이유 첫번째는 클래스별 메서드가 isSatisfiedBy로 시작은 하지만 이후 붙은 명칭은 각각 Period, Sequence가 됩니다. 
이렇게 되었을 때 메시지를 읽는 개발자들은 두 메시지가 같은 내용을 처리한 다는 내용을 곧바로 이해하기 어려워집니다. 

좋지 않은 이유 두번째는 메서드 수준에서 캡슐화를 위반하게 됩니다. 
이는 소스상에서 isSatisfiedByPeriod, isSatisfiedBySequence를 객체에서 사용하기 위해선 어떤 상황에 따라 분기해 두 객체의 인스턴스를 정적으로 선언해 메서드를 선언해야 사용을 할 수 있게 됩니다.
그리고 개별적인 이름으로 인해 메서드 명칭이 변경 될 경우 메시지를 전송하는 객체도 메서드명을 변경해야 하는 의존성이 생기기도 합니다. 

메서드를 명명하는 방법 두번째는 **어떻게가 아니라 무엇을 하는지 드러내라는 것**입니다. 
무엇을 하는지 드러내는 코드는 유연한 코드를 낳습니다. 유연한 코드를 낳을 수 있는 것은 이르게 내부 구현을 생각하지 않고 **협력안에서 책임을 생각해 메시지를 전송하는 목적을 먼저 생각**할 수 있도록 유도하기 때문입니다. 

```java 
public class PeriodCondition{
	public boolean isSatisfiedBy(Screening screening);
}

public class SequenceCondition{
	public boolean isSatisfiedBy(Screening screening);
}
```
결국 협력이라는 관점에서 PeriodCondition, SequenceCondition는 할인 조건을 확인하기 위한 책임을 가지고 있음으로 위의 isSatisfiedByPeriod, isSatisfiedBySequence는 isSatisfiedBy라는 공통된 메서드로 표현되는 것이 가장 좋은 방법이라 볼 수 있습니다. 

이렇게 함으로써 PeriodCondition, SequenceCondition 메서드를 하나로 표현하기 위한 DiscountCondition이라는 인터페이스를 만들 수 있게되고, 각 객체는 DiscountCondition을 상속받아 사용함으로써 같은 메시지를 사용한다는 것을 명확히 이해할 수 있게 됩니다. 

결국 의도를 드러내는 인터페이스는 수행방법에 관해선 언급하지말고 결과와 목적만을 포함하도록 클래스와 오퍼레이션을 만들라는 것으로 이해하면 됩니다. 

### 원칙의 함정
소프트웨어 설계엔 **절대적으로 올바른 법칙**이 존재하지 않습니다. 디미터 법칙과 묻지 말고 시켜라는 방식이 객체의 퍼블릭 인터페이스를 깔끔하고 유연하게 만든는 것은 사실이지만 그것이 모든 상황에 옳다고 표현할 수 는 없습니다.
그렇기에 원칙을 적용함에 있어 어떤 점을 주의할지 살펴보겠습니다.

#### 디미터 법칙은 하나의 도트(.)를 강제하는 규칙이 아니다. 
```java
IntStream.of(1,2,3,4,5).filter(x -> x > 10).distinct().count();
```
위의 코드는 얼핏보면 디미터 법칙을 어긴것 처럼 보입니다. 하지만 위의 코드는 디미터 법칙을 잘 지키고 있습니다. 왜냐하면 IntStream의 데이터를 filter를 거쳐 새로운 객체로 만들기 때문입니다.
이전에도 설명했지만 디미터 법칙을 어긴 것은 송신하는 객체가 수신하는 객체의 기능 구현에 알게 되어 결합도가 높아진다는 것이었습니다. 

하지만 위의 코드는 단지 기존의 객체가 다른 객체로 옮기는 것임으로 디미터의 법칙을 어긴다는 생각을 하면 안됩니다.

#### 결합도와 응집도의 충돌
```java 
public class PeriodCondition implements DiscountCondition{
	public boolean isSatisfiedBy(Screening screening){
		return screening.getStartTime().getDayOfWeek().equals(dayOfWeek)&&
		...  
	}
}
```
위의 코드는 해당 상영이 PeriodCondition이 할인 조건을 만족하는지 묻는 구현 코드입니다. 
이 코드는 얼핏 보면 PeriodCondition이 Screening의 내부 구조를 이용해 할인 기간 조건을 여부를 판단하기에   PeriodCondition과 Screening의 결합도가 높아진 것으로 보일 수 있습니다. 
그래서 결합도를 낮추고자 이 코드를 Screening 객체에서 구현하면 다음과 같이 표현될 수 있습니다. 

```java
public class Screening{
	public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime){
		return whenScreened.getDayOfWeek().equals(dayOfWeek) &&
		...  
	}
}

public class PeriodCondition implements DiscountCondition{
	public boolean isSatisfiedBy(Screening screening){
		return screening.isDiscountable(dayOfWeek, startTime, endTime);
	}
}
```
Screening이 기존의 PeriodCondition의 책임을 떠안게 되었습니다.
이로 인해 기존에 Screening은 예매하라는 메시지에만 집중하면 됐었지만 현재는 상영 할인의 기간 조건을 판단하는 책임까지 떠안게 됩니다. 
이로 인해 PeriodCondition이 수행해야될 책임까지 Screening이 떠안게 되면서 응집도가 낮아지게 되는 문제가 생깁니다.

결론적으로 협력안에 객체의 책임들이 가지는 기능을 고려하지 않은 체 원칙만을 지키며 구현을 하다보면 문제가 발생할 수 있다는 것을 알 수 있었습니다. 

### 명령-쿼리 분리 원칙
명령 쿼리 분리(Command-Query-Separation) 원칙은 퍼블릭 인터페이스에 오퍼레이션을 정의할 때 참고할 수 있는 지침을 제공하는데, 이해를 돕기 위해 먼저 몇가지 용어를 살펴보고자 합니다. 

**루틴(Routine)** 은 어떤 절차를 묶어 호출 가능하도록 이름을 부여한 기능 모듈이라고 합니다. 
루틴은 다시 **프로시저**와 **함수**로 구분할 수 있는데, 이들은 부수효과와 반환값의 유무라는 측면에서 명확히 구분을 할 수 있게 됩니다. 

**프로시저**는 정해진 절차에 따라 내부 상태를 변경하는 루틴의 한 종류로, 부수 효과를 발생시키지만 값을 반환할 수 없습니다. 
**함수**는 어떤 절차에 따라 필요한 값을 계산해 반환하는 루틴의 한 종류로, 부수 효과는 발생시키지 않지만 값을 반환할 수 있습니다. 

그리고 **명령**과 **쿼리**는 객체의 인터페이스 측면에서 프로시저와 함수를 부르는 또 다른 이름입니다. 
**명령**은 객체의 상태를 수정하는 오퍼레이션이며, 반환값을 가질 수 없습니다.
**쿼리**는 객체와 관련된 정보를 반환하는 오퍼레이션이며, 쿼리 상태를 변경할 수 없습니다. 

이렇듯 명령과 쿼리에 대한 간략한 개념을 설명했는데, 그렇다면 명령과 쿼리를 분리해서 얻게 되는 장점은 다음과 같습니다. 

#### 반복 일정의 명령과 쿼리 분리하기 
```java 
public class Event{
	private String subject;
	private LocalDateTime from;
	private Duration duration;

	public Event(String subject, LocalDateTime from, Duration duration){
	
	}

	public boolean isSatisfiedBy(RecurringSchedule schedule){
		if(from.getDayOfWeek() != schedule.getDayOfWeek() || 
		!from.toLocalTime().equals(schedule.getFrom()) ||
		!duration.equals(schedule.getDuration())){
			reschedule(schedule);
			return false;
		}

		return true;
	}

	private void reschedule(RecurringSchedule schedule){
		from = LocalDateTime(from.toLocalDate().plusDays(dayDistance(schedule)),schedule.getFrom());
		duration = schedule.getDuration();
	}

	private long daysDistance(RecurringSchedule schedule){
		return schedule.getDayOfWeek().getValue() - from.getDayOfWeek().getValue();
	}
}

public class RecurringSchedule{
	private String subject;
	private DayOfWeek dayOfWeek; 
	private LocalTime from;
	private Duration duration;

	public RecurringSchedule(String subject, DayOfWeek dayOfWeek, LocalTime from, Duration duration){
		...
	}

	public DayOfWeek getDayOfWeek(){
		return dayOfWeek; 
	}

	public LocalTime getFrom(){
		return from; 
	}

	public Duration getDuration(){
		return duration;
	}
}

public class Test{
	@Test
	void 기능테스트{
		RecurringSchedule schedule = new RecurringSchedule("회의", DayOfWeek.WEDNEDAY, LocalTime.of(10,30), Duration.ofMinutes(30));
		Event meeting = new Event("회의", LocalDateTime.of(2019, 5, 9, 10, 30));

		assert meeting.isSatisfied(schedule) == false;
		assert meeting.isSatisfied(schedule) == true;
	}
}
```
위의 코드에 대한 테스트를 설명하기 앞서 Event와 RecurringSchedule에 대해 설명하고자 합니다. 
Event는 특정일자에 실제로 발생하는 사건을 의미하고, RecurringSchedule은 반복되는 일정을 설명한 객체들입니다. 
그리고 위의 코드를 사용하는 시스템의 목적은 현재 일어나는 사건이 반복되는 일정인가를 확인하는 시스템입니다. 

이런 과정에서 기능 테스트를 진행한다고 했을 때 반복되는 일정인 수요일 10시 30분을 시작으로 30분간 회의를 진행한다는 내용을 담고 있습니다. 
그리고 사건이 발생되는 시간인 2019년 5월 9일(목요일) 10시 30분의 회의가 반복되는 일정과 같은지의 여부를 묻는 메서드를 작성합니다. 
당연하게도 두 schedule과 meeting이 만족하는지를 묻는 메서드의 결과는 false가 나와야 합니다. 하지만 한번더 결과를 돌렸을 때 true가 나오게 됩니다. 

이러한 결과가 나오는 것은 Event의 isSatisfiedBy 메서드의 구현에 문제가 생겨서인데 메서드의 구현은 반복되는 일정에 만족하지 못하면 일정을 재조정(reschedule)한다는 것에 있었습니다. 
결국 첫 결과가 올바른 값을 return 하더라도 두번째 결과에서 현재 meeting의 코드의 수정이 발생했고(일정이 다시 조율되고) 결국 true를 리턴하게 된다는 것입니다. 
이 방식의 문제는 명령과 쿼리를 명확히 분리하지 못했다는 것에서 발생하게 됩니다. 

```java 
public class Event{
	...
	public boolean isSatisfiedBy(RecurringSchedule schedule){
		if(from.getDayOfWeek() != schedule.getDayOfWeek() || 
		!from.toLocalTime().equals(schedule.getFrom()) ||
		!duration.equals(schedule.getDuration())){
			return false;
		}

		return true;
	}

	public void reschedule(RecurringSchedule schedule){
		from = LocalDateTime(from.toLocalDate().plusDays(dayDistance(schedule)),schedule.getFrom());
		duration = schedule.getDuration();
	}
	...
}
```
결국 문제를 해결하기 위해 일정 재조정 기능을 public으로 구현하고 필요에 따라 일정을 재조율하는 방식으로 변경했습니다. 
이 과정에서 기존의 private 였던 reschedule은 public으로 변경되었습니다. 어떻게 보면 응집도가 떨어진 것인가?라는 의문이 생길 수도 있고, 한편으로 클라이언트 개발자는 이 메서드의 기능을 한번 더 살펴보게 될 수도 있습니다. 

하지만 isSatisfiedBy는 Boolean이라는 return 값을 분명하게 가지고 있고 reschedule은 반환은 하지않지만 상태값만을 명확히 변경한다는 것을 코드를 통해 알 수 있습니다. 
즉 이 코드를 사용하는 대상자들도 쿼리와 명령을 분리함으로써 return값이 있고 없고의 유무를 따라 메서드의 기능을 유추할 수 있을 것이고, 이를 통해 코드를 조금 더 쉽게 파악할 수 있게 됩니다. (물론 정확히 어떤 내용인지는 코드를 봐야 알 것입니다.)
#### 명령-쿼리 분리와 참조 투명성
참조 투명성은 **어떤 표현식 e가 있을 때 e의 값으로 e가 나타내는 모든 위치를 교체하더라도 결과가 달라지지 않는 특성**을 말합니다.
예를 들어, f(1) = 3을 리턴하는 함수가 있다고 가정해보겠습니다. 
그렇다면 f(1) = 3에서 f(1)을 3으로 변경하더라도 전혀 식 자체에 문제가 발생하진 않습니다. 왜그러냐면 f(1)은 무조건 3을 리턴하도록 만들어져있기 때문입니다. 

이처럼 참조 투명성이 보장된 함수는 **불변성**을 가지고 부수효과를 발생시키지 않습니다. 
그리고 이렇게 참조 투명성을 통해 얻을 수 있는 이점은 모든 함수를 이미 알고 있는 하나의 결과값을 대체할 수 있어 쉽게 계산할 수 있다는 것과 식의 순서를 변경하더라도 식의 결과가 달라지지 않는 다는 장점을 가지고 있습니다.

# 읽고 느낀 점
시스템을 설계함에 있어 객체 지향적으로 설계하는 것은 올바른 일입니다. 
하지만 모든 순간에서 객체지향적으로 접근할 수 없다는 것을 배울 수 있었습니다. 
즉 지금까지 배우고 있는 객체지향적 접근은 **절대적인 법칙이 아닌 선택적으로 변할 수 있는 원칙**이라는 것을 알 수 있었습니다. 

그리고 위의 내용은 최근에 Object 책을 읽고 정리를 하며 많이 느끼고 있었던 부분 중 하나입니다. 
결합도를 낮추고 응집도를 높이고 캡슐화를 잘 갖춘 설계가 물론 대다수 좋은 설계이나 원칙을 고수하다 오히려 메서드간의 결합도가 높아지거나 기능상의 문제가 발생하는 사례도 볼 수 있었습니다. 

결국은 제가 여기까지 읽으면서 느낀 것은 모든 시스템은 협력하는 객체들의 기능이 정상적으로 작동하는 범위 내에서 최대한 객체 지향적으로 코드를 만들 필요가 있다는 것을 느낄 수 있었습니다.  
