# 간략한 내용 정리

## 객체 지향 설계 
객체 지향 설계는 올바른 객체에게 올바른 책임을 할당하면서 낮은 결합도와 높은 응집도를 가진 구조를 가진 구조를 창조하는 활동을 말합니다. 
이 정의에는 객체 지향 설계의 2가지 관점이 섞여 있는데, 첫번째는 객체지향 설계의 핵심이 **설계**라는 것과 두번째는 책임을 할당하는 작업이 응집도와 결합도와 같은 **설계 품질**과 연관되어 있다는 것입니다. 

설계는 변경을 위해 존재하고 훌륭한 설계란 합리적인 비용안에서 변경을 수용할 수 있는 구조를 만드는 것입니다.

그리고 훌륭한 설계를 만드는 것은 **객체의 행동에 초점**을 맞추어 설계를 하는 것이 좋은 설계 방법이 됩니다.
하지만 이 방법과는 다르게 **데이터를 중심**으로 설계를 했을 경우 변경에 유용하지않은 객체를 만들게 됩니다.

이러한 차이를 확인하기 위해 이번장에서는 **객체 중심의 설계**와 **데이터 중심의 설계**의 차이를 토대로 객체 지향적 설계에 대한 내용을 다루고자 합니다. 

### 데이터 중심의 영화 예매 시스템
데이터 중심의 설계는 객체가 내부에 저장해야하는 데이터가 무엇인가를 중점으로 설계를 진행하게 됩니다. 
```java
public enum MovieType{
  AMOUNT_DISCOUNT,  // 금액 할인 정책
  PERCENT_DISCOUNT, // 비율 할인 정책
  NONE_DISCOUTN     // 미적용
}

public class Movie{
  private String title;
  private Duration runningTime;
  private Money fee;
  private List<DiscountCondition> discountCondition;

  private MovieType movieType;
  private Money discountAmount;
  private double discountPercent;

  ... 
  // 이하 getter/setter 추가 
}
```
데이터 중심의 설계에선 discountAmount, discountPercent를 구분 지을 데이터가 필요합니다. 
그래서 사용된 것이 MovieType을 구분자로 사용해 영화 할인율을 계산하게 됩니다. 
그리고 캡슐화를 지키기위해 직접적으로 변수에 접근하지않고 변수에 접근하는 getter/setter를 추가해 직접적인 변수의 접근을 막습니다. 

```java
public enum DiscountConditionType{
  SEQUENCE, // 순번 조건 
  PERIOD    // 기간 조건
}

public class DiscountCondition{
  private DiscountConditionType type;
  private int sequence;
  
  private DayOfWeek dayOfWeek;
  private LocalTime startTime;
  private LocalTime endTime;

  ... 
  // 이하 getter/setter 추가
}
```
Movie와 마찬가지로 DiscountCondition 또한 순번 조건과 기간 조건을 구분할 데이터가 필요하게 됩니다.
이는 데이터의 구분 없이는 할인 조건을 표현할 수 없기 때문입니다. 
이하의 Screening, Reservation 또한 변수값과 getter/setter를 추가합니다. 

데이터 중심의 설계를 마친 후 완성된 설계는 다음과 같습니다. 
P106 그림 4.1 

이렇게 각 객체들을 getter와 setter로 구분한 다음 모든 기능 구현을 ReservationAgency에서 구현하게 됩니다. 
ReservationAgency는 상영되는 영화가 할인 조건에 일치한다면 할인 정책을 적용한다는 플로우를 추가하게 되는데 이 과정에서 역시 데이터를 구분자로 하여 flow를 처리하게 됩니다. 

### 설계 트레이드 오프
본격적으로 데이터 중심의 설계와 책임 중심의 설계를 비교하기 앞서 캡슐화, 응집도, 결합도에 대한 내용을 설명합니다. 

#### 캡슐화
캡슐화는 **외부에서 알 필요가 없는 정보를 감춤**으로써 대상을 단순화하는 추상화의 한 종류입니다.
즉 캡슐화는 **변경 가능성이 높은 구현**을 감추고 상대적으로 안정적인 **인터페이스**를 공개하는 추상화 방법이라 생각하면 됩니다. 
그리고 이것을 코드로 적용하기 위해선 객체 내부의 모든 변경 가능성이 있는 부분을 모두 숨기고 외부에선 특정 인터페이스를 통해서만 객체의 기능을 사용할 수 있게 변경해야 합니다.   

#### 응집도
응집도는 모듈에 포함된 요소들이 **하나의 목적을 위해 긴밀히 협력**한다면 **높은 응집도**를 가진다고 표현할 수 있습니다.
반대로 **모듈 내에 요소들이 서로 다른 목적을 추구**한다면 그 모듈은 **낮은 응집도**를 가진다고 표현할 수 있습니다. 
결국 내부 구현이 특정한 인터페이스에 의존하고 외부에선 그 인터페이스를 바라보는 형태가 응집도가 높다 말할 수 있을 것입니다. 
이것을 1장의 티켓 판매소 코드 예시로 보여드리면 다음과 같습니다. 
```java 
public class TicketOffice{
  // TicketSeller 호출
  public Ticket getTicket(){
    return tickets.remove(0);
  }

  // TicketSeller 호출
  public void plusAmount(Long amount){
    this.amount += amount;
  }

  // Main 객체 호출 << 응집도가 낮음을 설명하기 위해 임시로 추가
  public int print(){
    System.out.println(1);
  }
}
```
각 getTicket, plusAmount 함수는 TicketSeller가 처리하고 print 함수는 Main이 처리한다고 가정했을 때 TicketOffice의 모듈(메서드)은 각각 TicketSeller, Main이 기능을 사용하게 됩니다. 
즉 TicketOffice의 메서드들은 TicketOffice와는 별개로 서로 다른 객체들의 필요에 의해 사용됩니다.
이렇게 되면 TicketOffice내의 메서드들의 변경에 의해 연관된 객체들이 영향을 받게되고 시스템의 안정성을 저해하게 됩니다. 
이를 응집도가 낮다고 표현하며 **객체간의 필요 이상으로 기능을 나누고 있어** 캡슐화를 위반했다고 표현합니다. 

```java 
public class TicketOffice{
  public void sellTicketTo(Audience audience){
    plusAmount(audience.buy(getTicket()));
  }

  private Ticket getTicket(){
    return tickets.remove(0);
  }

  private void plusAmount(Long amount){
    this.amount += amount;
  }

}
```
반면에 위의 코드는 sellTicketTo라는 인터페이스를 구현하기 위해 getTicket, plusAmount가 사용되었습니다. 
이런 경우 메서드들이 필요한 인터페이스만을 노출하고 외적으로 변경 가능성이 높은 getTicket, plusAmount는 감추어 응집도를 높였다는 것을 확인할 수 있습니다. 
이렇게 되면 TicketOffice의 내부 구현 메서드들의 변경에도 공용 인터페이스를 사용하는 객체에겐 영향을 주진 않게 됩니다.
그리고 이와 같이 **객체간의 필요한 영역만 오픈했다는 점에서** 캡슐화를 잘되었다고 표현합니다.

#### 결합도
결합도는 어떤 모듈이 **다른 모듈에 대해 너무 자세한 부분까지 알고 있다면** 두 모듈은 **높은 결합도**를 가진다고 표현할 수 있습니다.
하지만 어떤 모듈이 **다른 모듈에 대해 꼭 필요한 인터페이스만 의존**하고 있다면 **낮은 결합도**를 가진다 표현할 수 있을 것입니다. 
결합도의 예시는 따로 없지만 위의 응집도가 낮은 TicketOffice 클래스를 본다면 TicketSeller와 Main이 TicketOffice와의 결합도가 높다는 것을 확인할 수 있습니다.
결국 결합도가 높다는 것은 **객체간의 필요 이상으로 기능을 나누고 있어** 캡슐화가 위반되었다는 것을 표현합니다. 

반대로 응집도가 높은 TicketOffice 클래스를 본다면 TicketSeller가 TicketOffice의 기능을 구현함에 있어 미치는 영향도는 없습니다. 
즉 TicketOffice의 **공용 인터페이스가 바뀌지 않는한 내부의 구현때문에** TicketSeller가 영향을 받을 일은 없다는 것입니다. 
이를 결합도가 낮다고 표현하며 **객체간의 필요한 영역만 오픈했다는 점에서** 캡슐화가 잘 이루어졌다고 표현합니다.

### 데이터 중심의 영화 예매 시스템 문제점
위의 코드들과 설명을 근거로 했을 때 데이터 중심의 설계의 가장 큰 문제점은 다음의 3가지입니다. 
1. 캡슐화 위반 
2. 높은 결합도 
3. 낮은 응집도
즉 객체들간의 **기능 공유가 필요 이상**으로 이루어지고 있다는 것입니다. 

이와 같은 문제 중점으로 데이터 중심의 설계에서 어떤 문제가 있는지를 적어보려 합니다.

#### 캡슐화 위반 
```java
public class Movie{
  private Money fee;

  // 다른 모든 객체에서 사용 가능
  public Money getFee(){
    return fee;
  }

  // 다른 모든 객체에서 사용 가능
  public void setFee(Money fee){
    this.fee = fee;
  }
}
```
위의 코드를 보면 인스턴스 변수인 fee는 private로 접근을 제한했다는 것을 알 수 있습니다. 
하지만 setFee와 getFee를 통해 어떤 객체에서라도 fee 변수에 접근하고 수정할 수 있게됩니다. 
이는 Movie가 스스로 자신의 변수를 관리하지 못하게 막고 필요 이상으로 다른 객체들이 기능을 사용하는 문제를 가져옵니다. 

이와 같은 문제는 객체간의 결합도를 높이고 Movie 자체의 응집도를 낮게 만들어 버립니다. 
결국 객체간 필요 이상의 기능 공유로 인해 캡슐화를 위반하고 있다는 것을 알 수 있습니다. 
이런 코드를 만들게 되는 것은 **객체의 기능을 명확히 정의하지 않고** 의도치 않은 다양한 상황에서 사용될 수도 있다는 **추측에 의한 설계 전략**으로 인한 것입니다.

결국 추측만으로 데이터를 설계하기에 개발자는 내부 상태를 최대한 많이 추가해야 한다는 압박감에 시달리게 됩니다.

- 여담으로 레거시 프로젝트의 VO를 활용한 프로젝트를 예시로 넣는 것도 좋은 방법이라 생각함 

#### 높은 결합도
```java 
public class Movie{
  ...
  private Money fee; // 2. fee와 관련된 모든 타입들의 변경이 발생  
  ...  
}

public class ReservationAgency{
  public Reservation reserve(Screening screening, Customer customer, int audienceCount){
    ...
    Money fee; // 1. fee의 타입의 변경이 생긴다면
    if(dicountable){
      ...
      fee = movie.getFee().minus(discountAmount).times(audienceCount);
    }else{
      fee = movie.getFee();
    }
    ... 
  }
}
```
이 코드에서 fee 변수의 타입을 변경한다고 가정해보겠습니다. 
그렇다면 fee와 연관된 Movie의 getFee 타입들 또한 변경이 되어야 합니다. 
이렇듯 객체들 간의 결합도가 높아지면 하나의 객체를 변경했을 때 다른 객체 또한 변경을 할 수 밖에 없습니다.

#### 낮은 응집도
```java
public enum MovieType{
  ...
  WOW_DISCOUNT // 임의로 할인 정책을 추가했습니다..^^
}

public class Movie{

  ... 
  private Money wowDiscountAmount; // // 임의로 할인 금액을 추가했습니다..^^
  ...
}

public class ReservationAgency{
  public Reservation reserve(Screening screening, Customer customer, int audienceCount){
    ...
    Money fee;
    if(dicountable){
      switch(movie.getMovieType){
        case AMOUNT_DISCOUNT:
	        ...
	        break;
	    case PERCENT_DISCOUNT:
			fee = movie.getFee().minus(discountAmount).times(audienceCount);
			break;
		case NONE_DISCOUNT:
			...	
			break;
		case WOW_DISCOUNT: // 임의로 할인 정책을 추가했습니다..^^ 
			... 
			break;
      }
    }else{
      fee = movie.getFee();
    }
    ... 
  }
}
```
예를 들어 reserve 메서드에 WOW_DISCOUNT를 추가했다고 가정해보겠습니다. 
현재 WOW_DISCOUNT를 추가함으로써 MovieType, Movie, ReservationAgency에서 코드 수정이 일어났습니다. 
결과적으로 하나의 수정에 다수의 객체가 변경이 일어나게 되고 변경으로 인해 시스템상에 문제가 발생했을 때 문제를 확정하기 어려워집니다.
이렇듯 하나의 객체가 여러 객체와의 의존성을 가지며 변경에 의해 모든 객체들이 영향을 받을 때 응집도가 낮다고 표현합니다. 

### 자율적인 객체를 향해
#### 스스로 자신의 데이터를 책임지는 객체
객체는 단순히 데이터를 제공하는 것이 아니라 협력에 참여해 자신의 책임을 정의하는 것이 중요합니다.
이는 협력을 위해 객체는 **어떤 데이터를 포함하고 어떤 데이터를 통해 어떤 오퍼레이션을 수행**해야할지에 대한 고민이 필요하다는 것입니다. 

다음은 이 관점에서 이전에 사용한 데이터 중심의 코드를 변경해보고자 합니다. 
```java 
public class DiscountCondition{
	private DiscountConditionType type;
	private int sequence;  
	private DayOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;
}
```
먼저 DiscountCondition의 데이터는 앞서 정의한 내용이 있음으로 위와 같은 데이터를 포함한다는 것을 알 수 있습니다.

```java 
public class DiscountCondition{
	...
	public DiscountConditionType getType(){
	   return type;
	}

	public boolean isDiscountable(DayOfWeek dayOfWeek, LoaclTime time){
	  ... 
	}

    public boolean isDiscountable(int sequence){
      ...
    }
}
```
다음은 데이터에 대해 수행할 수 있는 오퍼레이션은 무엇인지를 확인하는 것입니다. 
DiscountCondition은 순번 할인과 기간 할인을 할 수 있음으로 isDiscountable이라는 메서드를 오버로딩해 각각 순번 조건과 기간 조건의 값으로 구성합니다. 
기간 조건은 DayOfWeek, LocalTime을 인자값으로 순번 조건은 sequence를 인자값으로 구성합니다. 

```java
public class Movie{
  private String title;
  private Duration runningTime;
  private Money fee;
  private List<DiscountCondition> discountCondition;

  private MovieType movieType;
  private Money discountAmount;
  private double discountPercent;
}
```
Movie의 데이터 또한 앞서 정의한 내용이 있어 변수 값은 그대로 사용합니다.

```java
public class Movie{
	public MovieType getMovieType(){
		return movieType;
	}

	public Money calculateAmountDiscountFee(){
		... 
	}
	
	public Money calculatePercentDiscountFee(){
		... 
	}

	public Money calculateNoneDiscountFee(){
		...
	}
	
	public boolean isDiscountable(LocalDateTime whenSecreened, int sequence){
		... 
	}
}
```
가장 먼저 영화의 할인 정책을 적용할 메서드들을 정리합니다. 
각각은 고정 할인 금액에 대한 메서드, 비율 할인 금액에 대한 메서드, 할인 적용이 없는 메서드로 구분합니다. 
또한 할인을 받을 수 있을지 여부를 판단하는 메서드를 추가합니다. 

```java
public class Screening{
	private Movie movie;
	private int sequence;
	private LocalDateTime whenScreened;

	public Screening(Movie movie, int sequence, LocalDateTime whenScreened){
		...
	}

	public Money screening(int audienceCount){
		...
	}
	
}
```
상영 또한 객체를 데이터들의 구성은 이전과 동일합니다. 
그리고 상영에 앞서 calculateFee에서 상영되는 영화가 할인 정책별로 할인 조건이 있는지 여부를 확인하고 할인을 할 수 있으면 할인된 관람료를 리턴합니다. 만약 할인을 받을 수 없다면 그대로 영화 관람료를 리턴합니다.

```java
public class ReservationAgency{
	public Reservation reserve(Screening screening, Customer customer, int audienceCount){
		Money fee = screening.screening(audienceCount);
		return new Reservation(customer, screening, fee, audienceCount);
	}

}
```
위와 같이 각각의 객체가 자신의 역할을 수행할 수 있도록 변경을 했습니다

### 하지만 여전히 부족하다.

#### 캡슐화 위반, 높은 결합도, 낮은 응집도 
```java 
public class DiscountCondition{
	// 기간 조건 메서드
	public boolean isDiscountable(DayOfWeek dayOfWeek, LoaclTime time, LocalTime endTime){ // endTime이 추가되었음(임의로 추가함)
	  ... 
	}
}
```
예를 들어 DiscountCondition의 기간 조건메서드(isDiscountable)의 endTime 인수가 추가되었다 가정해보겠습니다. 

```java
public class Movie{	
	private DiscountCondition discountCondition;
	public boolean isDiscountable(LocalDateTime whenSecreened, int sequence){
		... 
		if(discountCondition.isDiscountable(dayOfWeek, time, endTime)){ // endTime이 추가되었음(임의로 추가함)
			...
		}
		...
	}
}

public class Screening{
	...
	
	public Money screening(int audienceCount){
		if(discountCondition.isDiscountable(dayOfWeek, time, endTime)){ // endTime이 추가되었음(임의로 추가함)
			...
		}
	}
}

```
그렇게 되면 조건 메서드를 사용하고 있는 Movie, Screening에 있는 isDiscountable의 코드를 변경해야 합니다.
결국 인자값 하나의 추가로 인해 모든 클래스의 영향이 생기게 됩니다.  
이처럼 DiscountCondition의 isDiscountable 메서드의 인자값이 추가됨에 따라 외부에 영향을 주는 **파급 효과가 커집**니다. 
파급 효과가 크다는 소리는 그만큼 **객체간의 결합도가 높다**는 말이고, 객체간의 결합도가 높기에 객체 하나가 자신의 역할만을 수행하지 못한다는 의미가 되므로 **응집도가 낮다**는 것을 알 수 있습니다. 
그리고 이러한 코드 변경은 결국 **시스템의 안정성을 저해**시키게 됩니다. 

#### 데이터 중심 설계의 문제점
결론적으로 데이터 중심 설계는 **캡슐화를 위반**할 수 밖에 없습니다. 
캡슐화를 위반하는 첫번째는 데이터 중심 설계는 **데이터와 기능을 분리하는 절차적 프로그래밍 방식**을 따르기 때문입니다. 

데이터 중심 설계는 객체를 그저 단순한 데이터의 집합체로 보기에 접근자와 수정자(getter/setter)를 과도하게 추가하게되고 이 과정에서 데이터 사용을 분리된 별도의 객체에서 구현하게 됩니다.
이는 상태와 행동을 하나의 단위로 캡슐화 하는 객체지향 패러다임을 반하게 됩니다. 

캡슐화를 위반하는 두번째는 데이터를 먼저 결정하고 데이터를 처리하는데 필요한 오퍼레이션을 나중에 결정하는 방식은 구현을 캡슐화하는데 실패한다는 것입니다. 
이는 데이터에 관한 지식이 객체의 인터페이스에 고스란히 드러나게되고, 캡슐화를 실패하게 됩니다. 

마지막으로 데이터 중심의 설계의 초점은 외부가 아닌 내부로 향하고 있다는 문제점이 있습니다.
실행 문맥에 대한 깊이 있는 고민 없이 객체가 관리할 데이터의 세부정보를 먼저 결정합니다. 
이는 객체의 구현이 이미 결정된 상태에서 다른 객체와의 협력 방법을 고민하기에 이미 구현된 객체의 인터페이스를 억지로 끼워맞출 수 밖에 없습니다. 

결국 **객체는 어떤 데이터를 포함하고 어떤 데이터를 통해 어떤 오퍼레이션을 수행**해야 하는지에 대한 관점에서 가장 큰 문제는 처음이 **협력**이 아닌 **데이터 중심**의 사고로 시작된다는 것에 있습니다. 

