# 간략한 내용 정리
## 부록B: 타입 계층의 구현
### 클래스를 이용한 타입 계층 구현
타입은 객체의 퍼블릭 인터페이스를 가르키기에 결과적으로는 클래스는 객체의 타입과 구현을 동시에 정의하는 것과 같습니다.
이것은 객체 지향 언어에서 클래스를 사용자 정의 타입(user-defined data type)이라고 부르는 이유입니다. 

10장에서 구현한 Phone 클래스를 보겠습니다.
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
		
		for(Call call: calls){
			result = result.plus(amount.times(call.getDuration()
												  .getSeconds() / 
											  seconds.getSeconds()
											  )
								  );
		}

		return result;
	}
}
```

해당 코드에서 Phone은 calculateFee 메시지에 응답할 수 있는 타입을 선언하는 동시에 객체 구현을 정의하고 있습니다. 
그리고 Phone의 경우 처럼 타입을 구현할 수 있는 방법이 단 한가지만 존재하는 경우에는 타입과 클래스를 동일하게 취급해도 무방합니다. 

하지만 타입을 구현할 수 있는 다양한 방법이 존재하는 순간부터 클래스와 타입은 갈라지기 시작합니다. 

Phone과 퍼블릭 인터페이스는 동일하지만 다른 방식으로 구현해야하는 객체가 필요하다고 가정해보겠습니다. 
가장 간단한 방법은 상속이 있습니다. 

```java 
public class NightlyDiscountPhone extends Phone{
	...
}
```

상속을 이용하면 부모 클래스의 구현과 퍼블릭 인터페이스 모두 물려받을 수 있기에 쉽게 구현할 수 있습니다.
하지만 부모클래스와 강하게 결합시키는 문제로 인해 가급적 추상 클래스를 상속받거나 인터페이스를 구현하는 방법을 권장합니다. 
### 인터페이스를 이용한 타입 계층 구현
<img src="https://github.com/OiKimiO/object/assets/53805469/ae749c7c-fdf6-42ed-bc35-b72ed5b2c277" style="width:100%;" />
간단한 게임을 개발한다고 가정해보겠습니다. 
게임 안에는 GameObject로 분류될 수 있는 다양한 객체들이 존재합니다. 
Displayable은 화면에서 표시할 수 있는 요소들을 정리했고, Effect는 효과음에 대한 기능을 담당합니다. 
Player, Monster는 화면에서 표시될 수 있는 기능을 상속받고, Sound는 효과음을 상속받습니다. 
특별히 폭발을 나타내는 Explosion은 화면에서 표시하고, 효과음을 내는 기능을 상속받아 표현합니다. 

Explosion의 경우 자바에서 구현할 경우 구현이 어렵습니다. 
왜냐하면 자바는 다중 상속을 구현하지 않기 때문입니다. 

그래서 클래스를 통한 상속이 아닌 인터페이스를 통해 타입 계층과 구현 계층을 구분해보려 합니다. 

<img src="https://github.com/OiKimiO/object/assets/53805469/8aedbd5e-b127-4a3d-9bd2-97c18cdc49f2" style="width:100%;" />
해당 설계를 보면 GameObject, Effect, Displayable, Collidable의 경우 타입에 해당합니다.
기존 설계에선 GameObject, Effect, Displayable가 클래스였지만 현재는 interface로 변경되었습니다. 
Collidable은 Displayable를 상속받는 구현체 중 충돌로 인한 이동에 제약을 받거나 피해를 입는 구현체들을 처리하는 interface입니다.

구현체들로는 Sound, Explosion, Player, Monster가 있습니다. 
행당 설계에서 이전 설계와의 가장 큰 차이점은 Explosion이 다양한 type을 사용할 수 있다는 것입니다. 
이럼으로써 이전과는 다르게 동적인 구현이 가능해졌습니다. 
또한 Collidable의 구현체로 Player, Monster가 있는데 이들은 Collidable 타입 하나를 계층으로 가집니다. 

이로써 interface가 가지는 특징이 2가지라는 것을 알 수 있습니다. 
#### 여러 클래스가 동일한 타입을 구현할 수 있다.
#### 하나의 클래스가 여러 타입을 구현할 수 있다.

이렇듯 타입 중심의 객체들의 객체를 설계하는 것을 통해 좀 더 객체 지향적으로 만들 수 있음을 확인할 수 있었습니다. 
### 추상 클래스를 이용한 타입 계층 구현
클래스 상속을 이용해 구현을 공유하면서도 결합도로 인한 부작용을 피하는 방법도 있는데, 그것은 추상클래스를 이용하는 것입니다. 

```java
public abstract class DiscountPolicy{
	...
	public Money calculateDiscountAmount(Screening screening){
		...
	}

	abstract protected Money getDiscountAmount(Screening screening);
}

public class AmountDiscountPolicy extends DiscountPolicy {
	@Override
	protected Money getDiscountAmount(Screening screening){
		...
	}
}

public class PercentDiscountPolicy extends DiscountPolicy {
	@Override
	protected Money getDiscountAmount(Screening screening){
		...
	}
}
```
구체 클래스로 타입을 정의해서 상속받는 방법과 추상 클래스로 타입을 정의해서 상속받는 사이에는 2가지 중요한 차이점이 있습니다. 

첫번째로 의존하는 대상의 추상화 정도가 다릅니다. 
Phone클래스의 경우 자식 클래스인 NightlyDiscountPhone의 calculateFee 메서드가 부모 클래스인 calculateFee 메서드와 강하게 결합됩니다. 
반면에 DiscountPolicy 추상 클래스는 getDiscountAmount 메서드를 자식 클래스인 AmountDiscountPolicy, PercentDiscountPolicy로 구현해 결합도를 낮출 수 있고, 변경 가능성이 높아집니다. 

두번째는 상속을 사용하는 의도가 다르다는 것입니다. 
Phone은 상속을 염두해둔 설계가 아닙니다.
Phone 설계자는 나중에 NightlyDiscountPhone이라는 개념이 추가될 것이라는 사실을 알지 못했습니다. 

반면에 DiscountPolicy는 상속을 염두에 두고 설계된 클래스입니다. 
DiscountPolicy는 추상 클래스이기에 직접 인스턴스를 생성할 수 없고, 클래스의 존재 목적은 자식 클래스를 추가하는 것입니다. 
이 클래스는 추상 메서드를 제공함으로써 상속 계층을 쉽게 확장할 수 있게 하고 결합도로 인한 부작용을 방지할 수 있는 안정망을 제공합니다. 

### 추상 클래스와 인터페이스 결합하기
상속의 경우 단일 상속만을 지원하고, 인터페이스만으로는 중복 코드를 제거하기 어렵다는 문제가 있습니다. 
그래서 효과적으로 인터페이스를 타입을 정의하고 특정 상속 계층에 국한된 코드를 공유할 필요가 있을 경우 추상클래스를 이용해 코드 중복을 방지할 수 있는 **골격 추상 클래스**를 이용하는 방법이 있습니다. 

이렇게 인터페이스와 추상 클래스를  함께 사용하는 경우 추상 클래스만 사용하는 방법에 비해 2가지 장점이 있습니다. 
	1. 다양한 구현 방법이 필요할 경우 새로운 추상 클래스를 추가해 쉽게 해결할 수 있습니다. 
	2. 이미 부모 클래스가 존재하는 클래스라고 하더라도 새로운 인터페이스를 추가함으로써 새로운 타입으로 쉽게 확장할 수 있습니다.

물론 무분별하게 골격 추상 클래스를 사용하면 코드의 복잡성을 더할 수 있으니 상황에 맞게 사용하는 것이 가장 좋은 방법이 될 것입니다. 
### 덕 타이핑 사용하기
덕 테스트는 객체가 어떤 인터페이스에 정의된 행동을 수행할 수만 있다면 그 객체를 해당 타입으로 분류해도 문제가 없습니다. 

덕 타이핑은 특정 클래스에 종속되지 않는 퍼블릭 인터페이스를 말합니다. 
여러 클래스를 가로지르는 이런 인터페이스는 클래스에 대한 값비싼 의존을 메시지에 대한 부드러운 의존으로 대체시킵니다. 
그리고 애플리케이션을 굉장히 유연하게 만들어주는 장점이 생깁니다. 

유연함이라는 장점을 가지고 있지만 동시에 단점이 있는데 그것은 컴파일 시점에 발견할 수 있는 오류를 실행시점으로 미뤄 안정성을 약화시킬 수도 있습니다. 
### 믹스인과 타입 계층
믹스인은 객체를 생성할 때 코드 일부를 섞어 넣을 수 있도록 만들어진 일종의 추상 서브클래스입니다. 
언어마다 구현 방법엔 차이가 있겠지만 믹스인을 사용하는 목적은 다양한 객체 구현 안에서 동일한 **행동**을 중복코드 없이 재사용할 수 있도록 만드는 것에 있습니다. 

스칼라에선 믹스인을 구현학 위해 **트레이트**를 사용하게 됩니다.  
트레이트를 이용해 간결한 인터페이스를 가진 클래스를 풍부한 인터페이스를 가진 클래스로 만들 수 있습니다. 

그리고 자바에선 스칼라의 트레이트와 비슷한 것이 **디폴트 메서드**입니다.
디폴트 메서드는 인터페이스에 디폴트 메서드가 구현돼있다면 이 인터페이스를 구현하는 클래스는 기본 구현을 가지고 있는 메서드를 구현할 필요가 없습니다. 
즉 코드를 재사용하면서도 인터페이스의 장점을 유지할 수있습니다. 

하지만 디폴트 메서드로 만들어졌단 이유만으로 public 메서드로 구현이 되어져버립니다. 
이것은 외부에 노출할 필요가 없는 메서드를 불필요하게 퍼블릭 인터페이스에 추가하는 결과를 낳게 됩니다. 

결국 의도하지 않게 퍼블릭 메서드를 추가하게 되어 기존 객체의 기능과는 별도의 메서드를 구현하게 될 수 있다는 단점도 존재합니다. 

## 부록C: 동적인 협력, 정적인 코드
프로그래머는 객체지향 프로그램을 작성하기 위해 동적 모델과 정적 모델을 염두해야 합니다. 
객체 지향 세계에서 동적 모델은 객체와 협력으로 구성되고, 정적 모델은 타입과 관계로 구성됩니다. 
그리고 두가지 모델 중 먼저 앞서 적용할 모델은 동적 모델이고 그 이후가 정적 모델이 됩니다. 

동적 모델을 먼저 적용하는 이유는 변경을  수용할 수 있는 코들르 만들기 위해서입니다. 
여기서 변경을 수용할 수 있는 코드란 결합도가 낮고, 중복이 없는 코드를 말합니다. 
이러한 목적을 달성하기 위해선 코드가 아닌 협력에 초점을 맞춰야 합니다. 

### 동적 모델과 정적 모델 
#### 행동이 코드를 결정한다
협력에 참여하는 객체의 행동이 객체의 정적 모델을 결정해야 합니다. 
예를 들어, 13장의 Bird, Penguin 상속에 있어 Bird에 fly메서드가 추가되어야 하는 상황이라면.. Penguin은 fly 메서드를 추가할 수 밖에 없게 됩니다. 

그렇지만 Penguin은 날 수 없는데 fly메서드를 추가하면 상속받은 fly메서드를 강제로 못쓰게 막을 수 밖에 없습니다. 

해당 예시를 통해서 알 수 있는 것은 정적 모델이 동적 모델이란 토대 없이는 완전해질 수 없다는 것입니다.
또한 정적 모델이 협력에 적합하지 않다며 정적 모델을 지속적으로 개선해가야 합니다.
#### 변경을 고려하라
객체가 제공하는 행동의 측면에서 적절하게 정적 모델을 고려하더라도 변경을 고려하지 않는다면 유지보수하기 어려운 코드가 만들어집니다. 
예시로 11장의 핸드폰 과금 시스템이 변경을 고려하지 않을 경우 발생하는 문제를 설명해주는 대표적인 예입니다. 

<img src="https://github.com/OiKimiO/object/assets/53805469/981363c5-403e-49d8-aba9-38cddbfef9be" style="width:100%;" />
해당 설계는 핸드폰 과금 시스템 초기 모델입니다. 
그림을 보면 알 수 있듯 모든 클래스들이 Phone 클래스를 상속받아 구현되고 있습니다. 
그리고 구현된 클래스들은 각 부모 클래스의 내용을 복사하여 표현되었습니다. 
이렇게 했을 때 구현은 쉽지만 변경이라는 측면과 중복 코드가 늘어난다는 점에 있어 좋은 코딩 방식이라 볼 순 없습니다. 

<img src="https://github.com/OiKimiO/object/assets/53805469/24ab818b-9c25-4b2d-8386-8489db7fddf5" style="width:100%;" />
해당 설계는 외부에 동일한 행동을 제공하면서도 코드 변경에 더 유연하게 대응할 수있게 만들었습니다. 
동일한 행동을 제공하는 정적 모델이 있다면 항상 현재의 설계에서 요구되는 벼경을 부드럽게 설계를 선택하는 것이 좋은 방법이 됩니다. 
### 도메인 모델과 구현 
#### 도메인 모델에 관하여 
도메인이란 사용자가 프로그램을 사용하는 대상 영역을 가르키며, 모델이란 지식을 선택적으로 단순화하고 의식적으로 구조화한 형태입니다. 
도메인 모델이란 사용자가 프로그램을 사용하는 대상여역에 대한 지식을 선택적으로 단순화하고 의식적으로 구조화한 형태를 말합니다. 

객체 지향 분석 설계에서 제안하는 지침 중 하나는 소프트웨어 도메인에 대해 고민하고 도메인 모델을 기반으로 소프트웨어를 구축하라는 것입니다. 
이 지침을 따르면 개념과 소프트웨어 사이의 표현적 차이를 줄일 수 있기에 수정하기 쉬운 소프트웨어를 만들 수 있습니다. 

물론 도메인 모델에 지나치게 집착하거나 도메인 모델의 초기 구조를 맹목적으로 따른는 코드를 작성한다면 변경하기 어려운 소프트웨어가 만들어질 확률이 높습니다. 

중요한 것은 객체들의 협력을 지원하는 코드 구조를 만드는 것입니다. 
도메인 개념을 충실히 따르는 코드가 목적이 아닙니다. 
#### 몬스터 설계하기
<img src="https://github.com/OiKimiO/object/assets/53805469/422cac7d-8a67-4458-95d0-ff4a88fac35e" style="width:100%;" />
해당 설계를 토대로 다음과 같이 코드를 만들었습니다. 

Monster는 추상 클래스로 getAttack을 추상 메서드로 만듭니다. 

```java
public abstract class Monster{
	private int health;

	public Monster(int health){
		this.health = health;
	}

	abstract public String getAttack();
}
```

Monster의 자식 클래스인 Dragon은 입에서 불을 내뿜어서 주인공을 공격할 수 있습니다. 

```java
public class Dragon extends Monster{
	public Dragon(){
		super(230);
	}

	@Override
	public String getAttack(){
		return "용을 불을 내뿜는다";
	}
}
```

Troll은 곤봉으로 주인공을 내리칠 수 있습니다. 

```java
public class Troll extends Monster{
	public Troll(){
	
	}

	@Override
	public String getAttack(){
		return "트롤은 곤봉으로 때린다";
	}
}
```

해당 설계는 현재까진 문제는 없으나.. 몬스터가 추가되는 경우엔 클래스를 추가해야되는 문제가 발생합니다. 

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/91020f49-8cc2-4722-97d9-dc478e7637d0" />

그래서 몬스터를 하나로 관리하기 위해 Breed 클래스를 만듭니다. 
Breed 클래스는 몬스터의 체력(health)과 공격 방식(attack)을 속성으로 포함하는 단순한 클래스입니다. 

```java
public class Breed{
	private String name;
	private int health;
	private String attack;

	public Breed(String name, int health, String attack){
		this.name = name;
		this.health = health;
		this.attack = attack;
	}

	public int getHealth(){
		return this.health;
	}

	public String getAttack(){
		return this.attack;
	}
}
```

각 몬스터 종류는 Breed 클래스의 인스턴스를 포함하는 Monster 클래스로 모델링 할 수 있습니다.

```java
public class Monster{
	private Breed breed;

	public Monster(Breed breed){
		this.breed = breed;
	}

	public String getAttack(){
		return breed.getAttack();
	}
}
```

이제 새로운 몬스터의 종류를 추가하는 것은 클래스를 추가하는 것이 아니라 새로운 Breed 인스턴스를 생성하고 Monster 인스턴스에 연결하는 작업으로 변경됩니다.

```java
Monster dragon = new Monster(new Breed("용", 230, "용은 불을 내뿜는다"));
Monster troll = new Monster(new Breed("트롤", 48, "트롤은 곤봉으로 때린다"));
```

이처럼 어떤 인스턴스가 다른 인스턴스 타입을 표현하는 방법을 TYPE OBJECT 패턴이라고 부릅니다. 
해당 코드에서 Breed의 인스턴스가 바로 Monster 타입을 구현하는 TYPE OBJECT에 해당합니다.
#### 행동과 변경을 고려한 도메인 모델 
초기에 고안한 도메인 모델은 좋은 출발점이 될 수는 있지만 객체의 행동과 변경이라는 요소를 고려하면 빠르게 그 가치가 떨어집니다. 
구현하거나 변경하기 더 쉬운 모델이 떠올랐다면 과감하게 초기 아이디어를 버려야합니다. 
객체 지향의 핵심은 객체 사이의 협력이며 설계는 변경을 위한 것입니다. 

또한 도메인 모델은 단순히 정적 모델의 형태를 띨 필요가 없으며 도메인 모델의 구조가 코드와 다를 필요가 없습니다. 
도메인 모델은 코드를 위한 것입니다. 
도메인 모델은 도메인 안에 존재하는 개념과 관계를 표현해야 하지만 최종 모습은 객체의 행동과 변경에 기반해야하며 코드의 구조를 반영해야 합니다. 

# 읽고 느낀 점
애플리케이션에 있어 가장 중요한 것은 행동이며, 그 행동은 변경을 쉽게 할 수 있도록 만들어져야한다는 것을 배울 수 있었습니다. 
앞으로도 좋은 애플리케이션을 개발하기위해 노력해야겠습니다.