# 간략한 내용 정리
## 일관성 있는 협력
잘 설계된 애플리케이션은 이해하기 쉽고 수정이 용이하며, 재사용 가능한 협력의 모임입니다.
객체지향 설계의 목표는 절적한 책을 수행하는 객체들의 협력을 기반으로 결합도가 낮고 재사용 가능한 코드 구조를 창조 하는 것입니다. 

객체 지향 패러다임의 장점은 설계를 재사용할 수 있다는 것입니다. 
하지만 재사용은 공짜로 얻어지진 않습니다. 
재사용을 위해선 객체들의 협력 방식을 일관성 있게 만들어야 합니다. 
일관성은 설계에 드는 비용을 감소시킵니다.

가능하면 유사한 기능을 구현하기 위해 유사한 협력 패턴을 사용하는 것이 좋습니다. 
예를 들어, 지금 보고 있는 코드가 얼마전에 봤던 코드와 유사한다는 사실을 아는 순간 새로운 코드가 직관적인 모습으로 다가오는 것을 느낄 수 있습니다. 
### 핸드폰 과금 시스템 변경하기

#### 기본 정책 확장
11장에서 구현한 핸드폰 과금 시스템의 요금 정책을 수정한다고 가정해보겠습니다. 
기존엔 기본 정책이 일반 요금제, 심야 요금제 2가지 종류가 있었지만 4가지 방식으로 확장을 해보겠습니다.

|유형|형식|예|
|---|---|---|
|고정요금 방식|A초당 B원|10초당 18원|
|시간대별 방식|A시부터 B시까지 C초당 D원|00시부터 19시까지 10초당 18원|
||B시부터 C시까지 C초당 E원|19시부터 24시까지 10초당 15원|
|요일별 방식|평일에는 A초당 B원|평일에는 10초당 38원|
||공휴일에는 A초당 C원|공휴일에는 10초당 19원|
|구간별 방식|초기 A분 동안 B초당 C원| 초기1분 동안 10초당 50원|
||A분 ~ D분까지 B초당 D원| 초기 1분 이후 10초당 20원|
||D분 초과 시 B초당 E원||
- 변경된 기본 정책의 종류

기본 정책을 구성하는 4가지 방식에 관해 간단히 살펴보겠습니다. 
**고정요금 방식**
일정 시간 단위로 동일한 요금을 부과하는 방식입니다.
모든 통화에 대해 동일하게 10초당 9원을 부과하는 방식이 고정요금 방식의 예에 해당됩니다.

**시간대별 방식**
하루 24시간을 특정한 시간 구간으로 나눈 후 구간별로 서로 다른 요금을 부과하는 방식입니다.
예를 들어, 0시 ~ 19시까지는 10초당 18원을, 19시부터 24시까지는 10초당 15원의 요금을 부과하는 방식입니다.
기존의 심야 할인 요금제는 밤 10시를 기준으로 요금을 부과한 시간대별 방식입니다. 

**요일별 방식**
요일별로 요금을 차등 부과하는 방식입니다. 
이 방식을 사용하면 월요일부터 금요일까지는 10초당 38원을, 토~일요일에는 10초당 19원을 부과하는 요금제를 만들 수 있습니다.

**구간별 방식**
전체 통화시간을 일정한 통화 시간에 따라 나누고 각 구간별로 요금을 차등 부과하는 방식입니다.
예를 들어, 통화 구간을 초기 1분과 1분 이후로 나눈 후 초기 1분 동안은 10초당 50원을, 그 이후에는 10초당 20원을 부고하는 방식이 구간별 방식에 해당합니다. 
만약 어떤 사용자의 전체 통화 시간이 60분이라면 처음 1분에 대해서는 10초당 50원이 부과되고 나머지 59분에 대해서는 10초당 20원의 요금을 부과합니다.

해당 기본 정책을 적용할 때 조합 가능한 모든 경우의 수를 나타낸 것입니다. 
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/81b1397c-0016-4e6f-bf30-f42d11225129" />

이번장에서 구현할 기본 정책의 클래스 구조입니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/681c7177-e6ab-4790-990b-e51c9393ca95" />
#### 고정요금 방식 구현하기
가장 간단한 고정요금 방식은 기존의 일반요금제와 동일하기에 기존의 RegularPolicy 클래스의 이름을 FixedFeePolicy로 수정하기만 하면 됩니다. 

```java
public class FixedFeePolicy extends BasicRatePolicy{
	private Money amount;
	private Duration seconds;

	public FixedFeePolicy(Money amount, Duration seconds){
		this.amount = amount;
		this.seconds = seconds;
	}

	@Override
	protected Money calculateCallFee(Call call){
		return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
	}
}
```
#### 시간대별 방식 구현하기
시간대별 방식에 따라 요금을 계산하기 위해서는 통화 기간을 정해진 시간대별로 나눈 후 각 시간대별로 서로 다른 계산 규칙을 적용해야 합니다. 

다음 그림은 시간대별 요금을 구하기 위한 내용을 표로 보여준 것입니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/a9302009-546d-443a-9e06-0155bb03f1df" />
만약 통화 시간별로 요금을 계산하기 위해선 18-19시까진 10초당 18원을 적용하면 되고, 19-20시까진 15원을 적용합니다.
이렇게 시간별로 요금을 적용할 수도 있으며, 날짜와 시간대별로도 요금을 적용할 수도 있습니다. 

여기서 중요한 것은 시간대별 요금을 계산하기 위해서는 통화의 시작 시간과 종료 시간뿐만 아니라 시작 일자와 종료일자도 함께 고려해야 한다는 것입니다. 
11장에선 심야할인 요금제의 경우엔 일자에 대한 고려가 없었기에 여러날에 걸친 통화에 대해서는 정확한 요금 계산이 불가능한 버그가 있었습니다. 

시간대별 방식을 구현하는데 있어 핵심은 규칙에 따라 통화 시간을 분할하는 것입니다.
이를 위해 기간을 편하게 관리할 수 있는 DateTimeInterval 클래스를 추가하도록 하겠습니다. 
DateTimeInterval은 시작 시간(from), 종료 시간(to)을 인스턴스 변수로 포함하여, 객체 생성을 위한 정적 메서드인 of, toMidnight, fromMidnight, during을 제공합니다. 

```java
public class DateTimeInterval{
	private LocalDateTime from;
	private LocalDatetime to;

	public static DateTimeInterval of(LocalDateTime from, LocalDatetime to){
		return new DateTimeInterval(from, to);
	}

	public static DateTimeInterval toMidnight(LocalDateTime from){
		return new DateTimeInterval(
			from,
			LocalDateTime.of(from.toLocalDate(),
							 LocalTime.of(23, 59, 59, 999_999_999))
		);
	}

	public static DateTimeInterval fromMidnight(LocalDateTime to){
		return new DateTimeInterval(LocalDateTime.of(to.toLocalDate(), 
													 LocalTime.of(0,0)
													 ),
									to
		);
	}

	public static DateTimeInterval during(LocalDate date){
		return new DateTimeInterval(LocalDateTime.of(date, 
													 LocalTime.of(0,0)),
									LocalDateTime.of(date, 23, 59, 59, 999_999_999))
		);
	}

	private DateTimeInterval(LocalDateTime from, LocalDateTime to){
		this.from = from;
		this.to = to;
	}

	public Duration duration(){
		return Duration.between(from, to);
	}

	public LocalDateTime getFrom(){
		return from;
	}

	public LocalDateTime getTo(){
		return to;
	}
}
```

기존의 Call 클래스는 통화 기간을 저장하기 위해 from과 to라는 2개의 LocalDateTime 타입의 인스턴스 변수를 포함하고 있습니다. 
```java
public class Call{
	private LocalDateTime from;
	private LocalDateTime to;
}
```

이제 기간을 하나의 단위로 표현할 수 있는 DateTimeInterval 타입을 사용할 수 있음으로 from과 to를 interval이라는 하나의 인스턴스 변수로 묶을 수 있습니다. 
```java
public class Call{
	private DateTimeInterval interval;

	public Call(LocalDateTime from, LocalDateTime to){
		this.interval = DateTimeInterval.of(from, to);
	}

	public Duration getDuration(){
		return interval.duration();
	}

	public LocalDateTime getFrom(){
		return interval.getFrom();
	}

	public LocalDateTime getTo(){
		return interval.getTo();
	}

	public DateTimeInterval getInterval(){
		return interval;
	}
}
```

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/09061b28-b887-4063-8e13-df7ff896a84d" />
그림과 같은 요금제를 전체 통화 시간을 일자와 시간 기준으로 분할해 계산하기 위해 요금 계산 로직을 다음과 같이 2개의 단계로 나눠 구현할 수 있습니다. 

##### 통화 기간을 일자별로 분리
통화 기간을 일자 단위로 나누는 작업의 정보 전문가는 통화 기간에 대한 정보를 잘 알고 있는 Call 객체입니다. 
하지만 Call은 통화 기간은 잘 알지 몰라도 기간 자체를 처리하는 방법에 대한 전문가는 아닙니다.
기간을 처리하는 전문가는 바로 DateTimeInterval입니다. 

##### 일자별로 분리된 기간을 다시 시간대별 규칙에 따라 분리한 후 각 기간 요금을 계산
시간대별로 분할하는 작업을 하는 정보 전문가는 TimeOfDayDiscountPolicy입니다. 

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/05ef5b0d-6f5f-4bec-8aab-cd70290ad106" />
전체 통화 시간을 분리하는 작업은 다음과 같이 TimeOfDayDiscountPolicy, Call, DateTimeInterval 사이의 협력으로 구현할 수 있습니다.

먼저 TimeOfDayDiscountPolicy는 통화 기간을 알고 있는 Call에게 일자별로 통화 기간을 분리할 것을 요청합니다. 
Call은 이 요청을 DateTimeInterval에게 위임합니다.
DateTimeInterval은 기간을 일자 단위로 분할한 후 분할된 목록을 반환합니다. 

Call은 반환된 목록을 그대로 TimeOfDayDiscountPolicy에게 반환합니다. 
TimeOfDayDiscountPolicy는 일자별 기간의 목록을 대상으로 루프를 돌리면서 각 시간대별 기준에 맞는 시작시간(from)과 종료시간(to)을 얻습니다. 

해당 기준을 토대로 TimeOfDayDiscountPolicy를 구현해보도록 하겠습니다. 
이 클래스에서 가장 중요한 것은 시간에 따라 서로 다른 요금  규칙을 정의하는 방법을 결정하는 것이니다. 
하나의 통화 시간대를 구성하는 데는 시작 시간, 종료 시간, 단위 시간, 단위 요금이 필요 합니다.

해당 그림은 각각 시작 시간, 종료 시간, 단위 시간, 단위 요금을 보여주는 리스트들입니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/55ddbb8a-a527-4b5b-85fe-4b2e7dfa347f" />

그리고 해당 리스트를 다음과 같은 코드를 구성하였습니다.
통화한 시간대별 금액을 결과값에 담아 return하는 기능이 있는 클래스입니다.
```java
public class TimeOfDayDiscountPolicy extends BasicRatePolicy{
	private List<LocalTime> starts = new ArrayList<>();
	private List<LocalTime> ends = new ArrayList<>();
	private List<LocalTime> durations = new ArrayList<>();
	private List<LocalTime> amounts = new ArrayList<>();

	@Override
	protected Money calculateCallFee(Call call){
		Money result = Money.ZERO;
		for(DateTimeInterval interval: call.splitByDay()){
			for(int loop=0; loop < starts.size(); loop++){
				result.plus(amounts.get(loop)
								   .times(Duration.between(from(interval, 
																starts.get(loop)), 
															 to(interval, 
																ends.get(loop))
															).getSeconds() / 												  durations.get(loop).getSeconds()
					)
				);
			}
		}
		return result;
	}

	private LocalTime from(DateTimeInterval interval, LocalTime from){
		return interval.getFrom()
					   .toLocalTime()
					   .isBefore(from)? from:interval.getFrom.toLocalTime();
	}

	private LocalTime to(DateTimeInterval interval, LocalTime to){
		return interval.getTo()
					   .toLocalTime()
					   .isAfter(to)? to:interval.getTo.toLocalTime();
	}
}
```

Call 클래스는 앞서 설계에서 확인했던 것 처럼 splitByDay() 메서드를 위임합니다.
```java
public class Call{
	...
	public List<DateTimeInterval> splitByDay(){
		return interval.splitByDay();
	}
}
```

DateTimeInterval 클래스의 splitByDay 메서드는 통화 기간을 일자별로 분할해서 반환합니다.
days메서드는 from과 to 사이에 포함된 날짜 수를 반환합니다. 
만약 days 메서드의 반환값이 1보다 크다면(여러날에 걸쳐 있는 경우라면) split 메서드를 호출해서 날짜 수만큼 분리합니다.
만약 days 메서드의 반환값이 1이라면(하루안의 기간이라면) 현재의 DateTimeInterval인스턴스를 리스트에 담아 그대로 반환합니다. 
```java
public class DateTimeInterval{
	public List<DateTimeInterval> splitByDay(){
		if(days() > 0){
			return splitByDay(days());
		}
		return Arrays.asList(this);
	}

	private long days(){
		return Duration.between(from.toLocalDate()
									.asStartOfDay(),
								to.toLocalDate()
								  .asStartOfDay()
		).toDays();
	}

	private List<DateTimeInterval> splitByDay(long days){
		List<DateTimeInterval> result = new ArrayList<>();
		addFirstDay(result);
		addMiddleDays(result, days);
		addLastDay(result);
		return result;
	}

	private void addFirstDay(List<DateTimeInterval> result){
		result.add(DateTimeInterval.toMidnight(from));
	}

	private void addMiddleDays(List<DateTimeInterval> result, long days){
		for(int loop=1; loop<days; loop++){
			result.add(DateTimeInterval.during(from.toLocalDate().plusDays(loop)));
		}
	}

	private void addLastDay(List<DateTimeInterval> result){
		result.add(DateTimeInterval.fromMidnight(to));
	}
}
```
#### 요일별 방식 구현하기
요일별 방식을 구성하는 규칙을 DayOfWeekDiscountRule이라는 하나의 클래스로 구현하려합니다.
DayOfWeekDiscountRule 클래스는 규칙을 정의하기 위해 필요한 요일의 목록(dayOfWeeks), 단위시간(duration), 단위 시간(duration), 단위 요금(amount)을 인스턴스 변수로 포함합니다. 
calculate 메서드는 파라미터로 전달된 interval이 요일 조건을 만족시킬 경우 단위 시간과 단위 요금을 이용해 통화 요금을 계산합니다. 

```java
public class DayOfWeekDiscountRule{
	private List<DayOfWeek> dayOfWeeks = new ArrayList<>();
	private Duration duration = Duration.ZERO;
	private Money amount = Money.ZERO;

	public DayOfWeekDiscountRule(List<DayOfWeek> dayOfWeeks, Duration duration, Money amount){
		this.dayOfWeeks = dayOfWeeks;
		this.duration = duration;
		this.amount = amount;
	}

	public Money calculate(DateTimeInterval interval){
		if(dayOfWeeks.contains(interval.getFrom().getDayOfWeek())){
			return amount.times(interval.duration().getSeconds() / duration.getSeconds());
		}
		return Money.ZERO;
	}
}
```

요일별 방식 역시 통화 기간이 여러 날에 걸쳐있을 수 있기에, 시간대별 방식과 동일하게 통화 기간을 날짜 경계로 분리하고 분리된 통화 기간을 요일별로 설정된 요금 정책에 따라 적절히 계산합니다. 
```java
public class DayOfWeekDiscountPolicy extends BasicRatePolicy{
	private List<DayOfWeekDiscountRule> rules = new ArrayList<>();

	public DayOfWeekDiscountPolicy(List<DayOfWeekDiscountRule> rules){
		this.rules = rules;
	}

	@Override
	protected Money calculateCallFee(Call call){
		Money result = Money.ZERO;
		for(DateTimeInterval interval: call.getInterval().splitByDay()){
			for(DayOfWeekDiscountRule rule: rules){
				result.plus(rule.calculate(interval));
			}
		}
		return result;
	}
}
```
#### 구간별 방식 구현하기
FixedFeePolicy, TimeOfDayDiscountPolicy, DayOfWeekDiscountPolicy는 응집도와 결합도 측면에 있어 문제가 없어 보입니다. 
하지만 각 객체들을 모아놓고 봤을 땐 설계에 일관성이 없다는 문제가 있습니다.

당장은 구간별 방식은 설계의 일관성을 따르지 않고 먼저 FixedFeePolicy 클래스를 상속받아 구현해보고자 합니다.
```java
public class DurationDiscountRule extends FixedFeePolicy{
	private Duration from;
	private Duration to;

	public DurationDiscountRule(Duration from, Duration to, Money amount, Duration seconds){
		super(amount, seconds);
		this.from = from;
		this.to = to;
	}

	public Money calculate(Call call){
		if(call.getDuration().compareTo(to) > 0){
			return Money.ZERO;
		}

		if(call.getDuration().compareTo(from) < 0){
			return Money.ZERO;
		}

		// 부모 클래스의 calculateFee(phone)은 Phone 클래스를 파라미터로 받는다.
		// calculateFee(phone)을 재사용하기 위해 데이터를 전달할 용도로 임시 Phone을 만든다.
		Phone phone = new Phone(null);
		phone.call(new Call(call.getFrom()
								.plus(from),
							call.getDuration()
								.compareTo(to)>0?call.gerFrom().plus(to):call.getTo()
					)
		);
		return super.calculateFee(phone);
	}
}
```

이제 여러개의 DurationDiscountRule을 이용해 DurationDiscountPolicy를 구현할 수 있습니다.
```java
public class DurationDiscountPolicy extends BasicRatePolicy{
	private List<DurationDiscountRule> rules = new ArrayList<>();

	public DurationDiscountPolicy(List<DurationDiscountRule> rules){
		this.rules = rules;
	} 

	@Override
	protected Money calculateCallFee(Call call){
		Money result = Money.ZERO;
		for(DurationDiscountRule rule: rules){
			result.plus(rule.calculate(call));
		}
		return result;
	}
}
```
### 설계에 일관성 부여하기
협력을 일관성있게 만들기 위해 다음과 같은 지침을 따르는 것이 도움이 됩니다.
	변하는 개념을 변하지 않는 개념으로부터 분리하라
	변하는 개념을 캡슐화하라

해당 2가지 원칙은 훌륭한 구조를 설계하기 위해 따라야 하는 기본 원칙이기도 합니다.
지금까지 이 책에서 설명했던 모든 원칙과 개념들 역시 대부분 변경의 캡슐화라는 목표를 향합니다.

언제나 그런 것 처럼 원칙을 이해하는 가장 좋은 방법은 코드를 살펴 보는 것이기에 다시 영화 예매 시스템 예제를 보겠습니다.
#### 조건 로직 대 객체 탐색
다음은 4장에서 절차적인 방식으로 구현했던 ReservationAgency의 기본 구조를 정리한 것입니다.
```java
public class RerservationAgency{
	public Reservation reserve(Screening screening, Customer customer, int audienceCount){
		for(DiscountCondition condition: movie.getDiscountConditions()){
			if(condition.getType() == DiscountConditionType.PERIOD){
				// 기간 조건인 경우
			}else{
				// 회차 조건인 경
			}
		}

		if(discountable){
			switch(movie.getMovieType()){
				case AMOUNT_DISCOUNT:
					// 금액 할인 정책인 경우
				case PERCENT_DISCOUNT:
					// 비율 할인 정책인 경우
				case NONE_DISCOUNT:
					// 할인 정책이 없는 경우
			}
		}else{
			// 할인 적용이 불가능한 경우
		}
	}
}
```
현재의 코드는 할인 조건과 할인 종류를 판단하는 두개의 if문이 존재하며 새로운 조건이 필요하면 또 다른 if문을 추가해야 합니다. 
이러한 방식은 변경 주기가 한 클래스안에 뭉쳐져 있기에 기존 코드의 내부를 수정해야 한다는 단점을 가집니다. 

반면 객체지향은 조금 다른 방법을 취하는데 다음의 Movie 클래스를 보면 할인 정책이 어떤 종류인지 확인하지 않고, 단순히 할인 정책과 관련된 메시지만을 전송한다.
```java
public class Movie{
	private DiscountPolicy discountPolicy;

	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
다형성은 바로 이런 조건 로직을 객체 사이의 이동으로 바꾸기 위해 객체 지향이 제공하는 설계기법입니다.
Movie는 discountPolicy가 자신의 요청을 잘 처리해줄 것이라 믿고 메시지를 전송할 뿐입니다.

DiscountPolicy와 할인 조건을 구현하는 DiscountCondition 사이의 협력 역시 마찬가지입니다.
DiscountPolicy는 DiscountCondition을 믿고 isSatisfiedBy 메시지를 전송합니다.
```java
public abstract class DiscountPolicy{
	private List<DiscountCondition> conditions = new ArrayList<>();

	public Money calculateDiscountAmount(Screening screening){
		for(DiscountCondition each: conditions){
			return getDiscountAmount(screening);
		}
		return screening.getMovieFee();
	}
}
```

그리고 해당 코드를 다 표현하지 못했지만 실제 협력관계를 표현하면 다음과 같습니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/9ee44f75-4d43-495e-925c-2713b599ce8c" />
해당 협력 관계에서 Movie는 현재의 할인 정책이 어떤 종류인지 판단하지 않습니다. 
단지 DiscountPolicy로 향하는 참조를 통해 메시지를 전달할 뿐입니다. 
또한 DiscountPolicy역시 할인 조건의 종류를 판단하지 않고 DiscountCondition을 전달할 뿐입니다.
이렇듯 객체지향 코드는 조건을 판단하지 않습니다. 

조건 로직을 객체 사이의 이동으로 대체하기 위해서는 커다란 클래스를 더 작은 클래스들로 분리해야 합니다. 
분리를 위한 기준은 변경의 이유와 주기입니다. 
클래스는 단 하나의 이유에 의해서만 변경되고 클래스 안의 모든 코드는 함께 변경되어야 합니다. 
간단하게 말해서 단일 책임 원칙을 따르도록 클래스를 분리해야 한다는 것입니다. 

Movie와 DiscountPolicy, DiscountConditon 사이의 협력 패턴은 변경을 기준으로 클래스를 분리함으로써 어떻게 일관성 있는 협력을 얻을 수 있는지를 보여줍니다. 
이 협력 패턴은 말 그대로 일관성 있기에 이해하기 쉽습니다. 
Movie, DiscountPolicy, DiscountConditon 사이의 협력 방식을 이해하면 새로운 할인 정책에 마주치더라도 설계를 쉽게 이해할 수 있습니다.

이렇듯 변경에 초점을 맞추고 캡슐화의 관점에서 설계를 바라보면 일관성 있는 협력 패턴을 얻을 수 있습니다. 
#### 캡슐화 다시 살펴보기
사람들은 캡슐화를 데이터 은닉이라고 합니다. 
데이터 은닉이란 외부에 공개된 메서드를 통해서만 객체의 내부에 접근할 수 있게 제한함으로써 객체 내부의 상태 구현을 숨기는 기법을 말합니다. 

하지만 캡슐화는 데이터 은닉 이상입니다. 
GOF의 조언에 따르면 캡슐화란 단순히 데이터를 감추는 것이 아니라 소프트웨어에서 변할 수 있는 **모든 개념을 감추는 것**입니다.

캡슐화의 가장 대표적인 예는 객체의 퍼블릭 인터페이스와 구현을 분리하는 것입니다.
객체를 구현한 개발자는 필요할 때 객체의 내부 구현 수정을 원하고, 객체와 협력하는 클라이언트 개발자는 객체의 인터페이스가 변하지 않기를 원합니다.
따라서 자주 변경되는 내부 구현을 안정적인 퍼블릭 인터페이스 뒤로 숨겨야 합니다.

<img src="https://github.com/OiKimiO/object/assets/53805469/a64de548-2669-4f37-b61d-e8d06703d022" style="width:100%;" />
##### 설계에 적용된 다양한 캡슐화 종류
###### 데이터 캡슐화
Movie 클래스의 인스턴스 변수 title의 가시성은 private 이기에 외부에서 직접 접근할 수 없습니다. 
###### 메서드 캡슐화
DiscountPolicy 클래스에 정의돼 있는 getDiscountAmount 메서드의 가시성은 protected입니다. 
클래스 외부에서는 이 메서드에 직접 접근할 수 없고 클래스 내부와 서브 클래스에서만 접근이 가능합니다. 
따라서 클래스 외부에 영향을 미치지 않고 메서드를 수정할 수 있습니다.
###### 객체 캡슐화
Movie 클래스는 DiscountPolicy 타입의 인스턴스 변수 discountPolicy를 포함합니다. 
이 인스턴스 변수는 private 가시성을 가지기에 Movie, DiscountPolicy 사이의 관계를 변경하더라도 외부에는 영향을 미치지 않습니다. 
객체 캡슐화는 합성을 의미하기도 합니다.
###### 서브타입 캡슐화
Movie는 DiscountPolicy에 대해서는 알고 있지만 AmountDiscountPolicy와 PercentDiscountPolicy에 대해서는 알지 못합니다. 
그러나 실제 실행 시점엔 클래스들의 인스턴스와 협력할 수 있습니다.
서브타입 캡슐화는 다형성에 기반이 됩니다.

캡슐화는 단지 데이터 은닉만을 말하지는 않습니다.
수정으로 인한 파급 효과를 제어할 수 있는 기법이 모두 캡슐화의 일종입니다.

#####  캡슐화를 통해 협력을 일관성 있게 만드는 가장 일반적인 방법
그리고 캡슐화를 통해 협력을 일관성 있게 만드는 가장 일반적인 방법은 **서브타입 캡슐화**와 **객체 캡슐화**를 조합하는 것이고, 적용하는 방법은 다음과 같습니다.
###### 변하는 부분을 분리해서 타입 계층을 만든다.
변하지 않는 부분으로 부터 변하는 부분을 분리합니다. 
변하는 부분들의 공통적인 행동을 추상 클래스나 인터페이스로 추상화한 후 변하는 부분들이 이 추상 클래스나 인터페이스를 상속받게 만듭니다.
이제 변하는 부분은 변하지 않는 부분의 서브타입이 됩니다.
###### 변하지 않는 부분의 일부로 타입 계층을 합성한다.
앞서 구현한 타입 계층을 변하지 않는 부분에 합성합니다. 
변하지 않는 부분에서는 변경되는 구체적인 사항에 결합돼서는 안됩니다. 
의존성 주입과 같이 결합도를 느슨하게 유지할 수 있는 방법을 이용해 오직 추상화에만 의존하게 만듭니다. 

### 일관성 있는 기본 정책 구현하기
#### 변경 분리하기
일관성 있는 협력을 반들기 위한 첫번째 단계는 변하는 개념과 변하지 않는 개념을 분리하는 것입니다. 
변경을 분리하기 위해선 기본 정책의 요구사항을 정리해 볼 필요가 있습니다. 

표14.2는 기본 정책을 구성하는 각 방식별 요금 규칙을 정리한 것입니다. 
표시된 시간대별, 요일별, 구간별 방식의 패턴을 살펴보면 어느정도 유사한 형태를 가진다는 것을 알 수 있습니다.

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/c520ac60-1003-4d00-8f7e-3ae726a15319" />

먼저 시간대별, 요일별, 구간별 방식의 공통점은 각 기본 정책을 구성하는 방식이 유사하다는 점입니다. 
	기본 정책은 한개 이상의 **규칙**으로 구성됩니다.
	하나의 **규칙**은 **적용 조건**과 **단위 요금**의 조합입니다.  

이해를 돕기 위해 그림 14.14.를 보겠습니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/196f36e0-95a4-4bb3-ad23-be87fdd71d0e" />
**단위 요금**은 말 그대로 단위시간당 요금 정보를 의미합니다. 
**적용 조건**은 통화 요금을 계산하는 조건을 의미합니다. 
**단위 요금**과 **적용 조건**이 모여 하나의 **규칙**을 구성합니다.
이를 통해 시간대별, 요일별, 구간별 방식은 **하나 이상의 규칙들의 집합**이라는 공통점을 가집니다.

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/d7b75e0c-9844-4268-ae51-6f118a094e1c" />
**하나 이상의 규칙들의 집합**이라는 공통점을 가지지만 실제 적용 조건의 세부적인 내용은 서로 차이가 있습니다.

공통점은 변하지 않는 부분입니다. 차이점은 변하는 부분입니다.
우리의 목적은 변하지 않는 부분과 변하는 것을 분리하는 것임으로 변하지 않는 **규칙**으로부터 변하는 **적용조건**을 분리해야 합니다.
#### 변경 캡슐화하기
해당 설계에서 변하지 않는 것은 **규칙**이고, 변하는 것은 **적용 조건**입니다.
따라서 **규칙**으로부터 **적용 조건**을 분리해서 추상화한 후 시간대별, 요일별, 구간별 방식을 이 추상화의 서브타입으로 만듭니다.
이것이 서브 타입 캡슐화입니다. 

그 후에 규칙이 적용조건을 표현하는 추상화를 합성 관계로 연결합니다.
그러면 객체 캡슐화가 이루어집니다.

해당 그림은 서브 타입 캡슐화와 객체 캡슐화를 보여준 그림입니다.
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/f33380a5-9085-4254-a2a6-99cec258244f" />
해당 설계를 보며 변하지 않는 부분은 기본 정책이 **여러 규칙**들의 **집합**이며, 하나의 **규칙**은 **적용 조건**과 **단위 요금**으로 구성된다는 것입니다.
이 관계는 BasicRatePolicy, FeeRule, FeeCondition의 조합으로 구현됩니다. 

또한 FeeCondition의 서브 타입인 TimeOfDayFeeCondition, DayOfWeekFeeCondition, DurationFeeCondition으로 구현됩니다.

FeeRule은 FeeCondition에 대해서만 의존하기에 **적용 조건**이 변경되더라도 영향을 받지 않습니다.
즉, **적용 조건**이라는 변경에 대해 캡슐화되어 있습니다.
#### 협력 패턴 설계하기
해당 그림은 **변하지 않는 요소**와 **변하는 부분의 추상화**를 설계로 표현한 것입니다.
<img src="https://github.com/OiKimiO/object/assets/53805469/3d41233d-f82d-4705-b54c-93a5038d5208" style="width:100%;" />

그림 14.18은 추상화들이 참여하는 협력을 나타낸 것입니다.
협력은 BasicRatePolicy가 calculateFee 메시지를 수신했을 때 시작됩니다. 
BasicRatePolicy의 calculateFee 메서드는 인자로 전달받은 통화 목록(`List<Call>` 타입)의 전체 요금을 계산합니다.

BasicRatePolicy는 목록에 포함된 Call별로 FeeRule의 calculateFee 메서드를 실행합니다.
하나의 BasicRatePolicy는 하나 이상의 FeeRule로 구성되기에 Call 하나당 FeeRule에 다수의 calculateFee 메시지가 전송됩니다.
<img src="https://github.com/OiKimiO/object/assets/53805469/c6f43976-d366-4b7d-bed1-d094b3c64b4f" style="width:100%;" />

FeeRule은 하나의 Call에 대해 요금을 계산하는 책임을 수행합니다. 
현재 FeeRule은 단위 시간당 요금은 feePerDuration과 요금을 적용할 조건을 판단하는 적용 조건인 FeeCondition 인스턴스를 알고 있습니다.

하나의 Call 요금을 계산하기 위해선 두개의 작업이 필요합니다. 
하나는 전체 통화 시간을 각 **규칙**의 **적용조건**을 만족하는 구간들로 나누는 것입니다.
다른 하나는 이렇게 분리된 통화 구간에 단위 요금을 적용해 요금을 계산하는 것입니다. 

그리고 해당 조건을 적용한 그림은 아래와 같습니다.
<img src="https://github.com/OiKimiO/object/assets/53805469/b44f9d9f-02c6-48f9-a152-684dd18de6fd" style="width:100%;" />
시간대별 방식일 경우 해당 날짜의 시간별로 통화 요금을 구분하고, 구분한 요금을 다시 같은 규칙에 해당하는 요금끼리합쳐 통화 시간을 계산합니다. 

해당 그림은 FeeRule이 FeeCondition의 인스턴스에게 findTimeIntervals 메시지를 전송합니다.
findTimeIntervals는 통화 기간 중에서 적용조건을 만족하는 구간을 가지는 DateTimeInterval의 List를 반환합니다. 
FeeRule은 feePerDuration 정보를 이용해 반환받은 기간만큼의 통화 요금을 계산한 후 반환합니다.
<img src="https://github.com/OiKimiO/object/assets/53805469/608466b0-dc02-43b7-936f-0734f7a37e8a" style="width:100%;" />
#### 추상화 수준에서 협력 패턴 구현하기
먼저 적용 조건을 표현하는 추상화인 FeeCondition에서 시작하겠습니다.
FeeCondition은 findTimeIntervals라는 단 하나의 오퍼레이션을 포함하는 간단한 인터페이스입니다. 
이 오퍼레이션은 인자로 전달된 Call의 통화 기간 중 **적용조건**을 만족하는 기간을 구한 후 List에 담아 반환합니다. 
```java
public interface FeeCondition{
	List<DateTimeInterval> findTimeIntervals(Call call);
}
```

FeeRule은 단위 요금(feePerDuration)과 적용 조건(feeCondition)을 저장하는 2개의 인스턴스 변수로 구성됩니다. 
FeeRule의 calculateFee 메서드는 FeeCondition에게 findTimeIntervals 메시지를 전송해 조건을 만족하는 시간의 목록을 반환받은 후 feePerDuration의 값을 이용해 요금을 계산합니다. 
```java
public class FeeRule{
	private FeeCondition feeCondition;
	private FeePerDuration feePerDuration;

	public FeeRule(FeeCondition feeCondition, FeePerDuration feePerDuration){
		this.feeCondition = feeCondition;
		this.feePerDuration = feePerDuration;
	}

	public Money calculateFee(Call call){
		return feeCondition.findTimeIntervals(call)
						   .stream()
						   .map(each -> feePerDuration.calculate(each))
						   .reduce(Money.ZERO, (first, second) -> first.plus(second));
	}
}
```

FeePerDuration 클래스는 **단위 시간당 요금**이라는 개념을 포함하고 이 정보를 이용해 일정 기간 동안 의 요금을 계산하는 calculate 메서드를 구현합니다.
```java
public class FeePerDuration{
	private Money fee;
	private Duration duration;

	public FeePerDuration(Money fee, Duration duration){
		this.fee = fee;
		this.duration = duration;
	} 

	public Money calculate(DateTimeInterval interval){
		return fee.times(Math.ceil((double) interval.duration()
													.toNanos()/
									duration.toNanos()));
	}
}
```

이제 BasicRatePolicy가 FeeRule의 컬렉션을 이용해 전체 통화 요금을 계산하도록 수정할 수 있습니다.
```java
public class BasicRatePolicy implements RatePolicy{
	private List<FeeRule> feeRules = new ArrayList<>();

	public BasicRatePolicy(FeeRule ... feeRules){
		this.feeRules = Arrays.asList(feeRules);
	}

	@Override
	public Money calculateFee(Phone phone){
		return phone.getCalls()
					.stream()
					.map(call -> calculate(call))
					.reduce(Money.ZERO, (first, second) -> first.plus(second));
	}

	private Money calculate(Call call){
		return feeRules.stream()
					   .map(rule -> rule.calculateFee(call))
					   .reduce(Money.ZERO, (first, second) -> first.plus(second));
	}
}
```
지금까지 구현한 클래스와 인터페이스는 모두 변하지 않는 추상화에 해당합니다. 
이 요소들을 조합하면 전체적인 협력 구조가 완성됩니다. 
다시 말해 변하지 않는 요소와 추상적인 요소만으로도 요금 계산에 필요한 전체적인 협력 구조를 설명할 수 있습니다.
#### 구체적인 협력 구현하기
현재 요금제가 시간대별 정책인지, 요일별 정책인지, 구간별 정책인지를 결정하는 기준은 FeeCondition을 대체하는 객체의 타입이 무엇인가에 달려있습니다. 
##### 시간대별 정책
시간대별 정책의 적용 조건을 구현하는 TimeOfDayFeeCondition을 코드로 작성해보겠습니다.

```java
public class TimeOfDayFeeCondition implements FeeCondition{
	private LocalTime from;
	private LocalTime to;

	public TimeOfDayFeeCondition(LocalTime from, LocalTime to){
		this.from = from;
		this.to = to;
	}

	@Override
	public List<DateTimeInterval> findTimeIntervals(Call call){
		return call.getInterval()
				   .splitByDay()
				   .stream()
				   .filter(each -> from(each).isBefore(to(each)))
				   .map(each -> DateTimeInterval.of(
					   LocalDateTime.of(each.getFrom().toLocalDate(), from(each)),
					   LocalDateTime.of(each.getTo().toLocalDate(), to(each))
				   ))
				   .collect(Collectors.toList());
	}

	private LocalTime from(DateTimeInterval interval){
		return interval.getFrom()
					   .toLocalTime()
					   .isBefore(from)?from:interval.getFrom().toLocalTime();
	}

	private LocalTime to(DateTimeInterval interval){
		return interval.getTo()
					   .toLocalTime()
					   .isAfter(to)?to:interval.getTo().toLocalTime();
	}
}
```
해당 코드에서 findTimeIntervals 메서드는 인자로 전달된 Call의 통화 기간중에서 TimeOfDayFeeCondition의 from과 to 사이에 포함되는 시간 구간을 반환합니다. 
이를 위해 DateTimeInterval의 splitByDay 메서드를 호출해 날짜별로 시간 간격을 분할한 후 from과 to 사이의 시간대를 구하면 됩니다.
##### 요일별 정책
요일별 정책의 적용 조건으 DayOfWeekFeeCondition 클래스로 구현합니다. 
요일별 정책은 평일이나 주말처럼 서로 인접한 다수의 요일들을 하나의 단위로 묶어서 적용합니다.
```java
public class DayOfWeekFeeCondition implements FeeCondition{
	private List<DayOfWeek> dayOfWeeks = new ArrayList<>();

	public DayOfWeekFeeCondition(DayOfWeek ... dayOfWeeks){
		this.dayOfWeeks = Arrays.asList(dayOfWeeks);
	}

	@Override
	public List<DateTimeInterval> findTimeIntervals(Call call){
		return call.getInterval()
				   .splitByDay()
				   .stream()
				   .filter(each -> dayOfWeeks.contains(each.getFrom().getDayOfWeek()))
				   .collect(Collectors.toList());
	}
}
```
##### 구간별 정책
협력을 일관성있게 만들기 위해 DurationFeeCondition 클래스를 추가한 후 findTimeIntervals 메서드를 오버라딩합니다. 
```java
public class DurationFeeCondition implements FeeCondition{
	private Duration from;
	private Duration to;

	public DurationFeeCondition(Duration from, Duration to){
		this.from = from;
		this.to = to;
	}

	@Override
	public List<DateTimeInterval> findTimeIntervals(Call call){
		if(call.getInterval().duration().compareTo(from) < 0){
			return Collections.emptyList();
		}
		return Arrays.asList(DateTimeInterval.of(
			call.getInterval().getFrom().plus(from),
			call.getInterval().duration().compareTo(to) > 0?
				call.getInterval().getFrom().plus(to):
				call.getInterval().getTo()
		));
	}
}
```

구체적으로 협력을 구현하면서 이전과는 다르게 일관성 있는 코드를 만들 수 있다는 장점을 얻었습니다.
물론 일관성이라는 이면에는 코드를 짜는 방식의 제약이라는 부분이 있지만 그럼에도 코드의 재사용성이나 테스트하는 코드의 양이 감소되는 효과를 가질 수 있었습니다. 

또한 유사한 기능에 대해 유사한 협력 패턴을 적용하는 것은 객체지향 시스템에서 개념적 무결성을 유지할 수 있는 가장 효과적인 방법입니다.
#### 협력 패턴에 맞추기
여러 개의 **규칙**으로 구성되고 **규칙**이 **적용 조건**과 **단위 요금**의 조합으로 구성되는 시간대별, 요일별, 구간별 정책과 달리 고정 요금 정책은 기존의 협력 방식에서 벗어날 수 밖에 없습니다.

하지만 최대한 설계를 맞추어 패턴을 적용하는 것이 일관성을 유지할 수 있음으로 다음과 같이 코드를 구성합니다.
```java
public class FixedFeeCondition implements FeeCondition{
	@Override
	public List<DateTimeInterval> findTimeIntervals(Call call){
		return Arrays.asList(call.getInterval());
	}
}
```

#### 패턴을 찾아라
애플리케이션에서 유사한 기능에 대한 변경이 지속적으로 발생하고 있다면 변경을 캡슐화할 수 있는 적절한 추상화를 찾은 후, 이 추상화에 변하지 않는 공통적인 책임을 할당합니다. 
현재의 구조가 변경을 캡슐화하기엔 적합하지 않다면 코드를 수정하지 않고도 원하는 변경을 수용할 수 있도록 협력과 코드를 리팩터링 합니다. 

변경을 수용할 수 있는 적절한 역할과 책임을 찾다보면 협력의 일관성이 서서히 윤곽을 드러낼 것입니다. 

협력을 일관성 있게 만든다는 것은 유사한 변경을 수용할 수 있는 협력 패턴을 발견하는 것입니다. 
# 읽고 느낀 점
#### 좋은 설계1: 변하지 않는것, 추상화를 통해 협력 관계를 만들고 일관성을 유지하라
이번 장을 읽으며 좋은 설계란 무엇일까에 대한 생각을 많이 해볼 수 있었습니다.
그리고 이번장에선 좋은 설계는 일관성있는 설계라고 합니다.

그런데 프로젝트를 접하다보면 너무 다양한 방식의 코드들을 접하게 될 때가 참 많습니다..ㅎㅎ
그 이유는 너무 당연하게도 다양한 사람들이 해당 프로젝트를 접하게 되며 자신만의 방식으로 코딩을 하게 되기 때문이라고 봅니다.

지금 제가 맡고 있는 GPS 관제 프로젝트도 마찬가지입니다.
그리고 책에선 이런 일관성 없는 프로젝트들에는 먼저 변하지 않는 것과 추상화를 할 수 있는 부분을 찾아볼 것을 권장합니다.

해당 부분을 찾다 보면 협력 관계를 구축 할 수 있고 이에 따라 일관성있는 좋은 설계를 할 수 있게 되기때문입니다.
이 내용을 읽으며 다음 레거시 프로젝트 개선건에서 적용해봐야겠단 생각을 많이 하게 되는 것 같습니다.

물론 모든 것에 업무 파악을 명확히 하는 것이 중요한 요소일 것입니다.
#### 좋은 설계2: 지속적인 개선
또한 책에선 협력 관계가 명확한 일관성 있는 코드라 할지라도 지속적으로 개선할 것을 권장합니다. 
이러한 이유는 설계 초기 단계에는 모든 요구사항을 미리 예상할 수 없기 때문입니다.
만약 현재의 협력 패턴이 변경의 무게를 지탱하기 어렵다면 변경을 수용할 수 있는 협력 패턴을 향해 과감하게 리팩터링 할 것을 권장합니다. 

저 또한 제가 할 수 있는 만큼의 개선사항과 적용점을 고민하고, 더 좋은 시스템을 만들기 위해 노력해야겠습니다.

