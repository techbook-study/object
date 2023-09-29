# 간략한 내용 정리
## 서브클래싱과 서브타이핑
상속의 용도는 2가지입니다.
1. **타입계층**을 구현 
2. **코드 재사용**

그리고 2가지 중에서도 일차적인 목표는 코드 재사용이 아니라 타입 계층을 구현하는 것입니다. 
코드를 재사용하는 상속은 코드를 쉽게 재사용할 수 있지만 부모 클래스와 자식 클래스를 강하게 결합시켜 설계의 변경과 진화를 방해합니다. 
반면 타입 계층을 목표로 사용하면 다형적으로 동작하는 객체들의 관계에 기반해 확장 가능하고 유연한 설계를 얻을 수 있습니다. 

결론은 동일한 메시지에 대해 서로 다르게 행동할 수 있는 다형적인 객체를 구현하기위해선 개게체의 행동을 기반으로 타입 계층을 구성해야 합니다. 

### 타입
#### 개념관점의 타입
개념 관점에서 타입이란 우리가 인지하는 세상의 사물의 종류를 의미합니다. 
어떤 대상이 타입으로 분류될 때 그 대상을 타입의 인스턴스라고 부릅니다. 자바, 루비, 자바스크립트, C는 프로그래밍 언어의 인스턴스입니다. 

일반적으로 타입의 인스턴스를 객체라고 부릅니다. 
지금까지의 설명을 통해 타입이 심볼, 내연, 외연의 3가지로 구분된다는 사실을 알 수 있습니다.
심볼(symbol): 타입에 이름을 붙인 것입니다. 앞에서 **프로그래밍 언어**가 타입의 심볼에 해당
내연(intension): 타입의 정의로서 타입에 속하는 객체들이 가지는 공통적인 속성이나 행동
외연(extension): 타입에 속하는 객체들의 집합
#### 프로그래밍 언어 관점의 타입
프로그래밍 언어 관점에서 타입은 연속적인 비트에 의미와 제약을 부여하기 사용되며, 2가지 목적을 위해 사용됩니다.
##### 타입에 수행될 수 있는 유효한 오퍼레이션의 집합을 정의한다.
##### 타입에 수행되는 오퍼레이션에 대해 미리 약속된 문맥을 제공한다. 
정리하면 타입은 적용 가능한 오퍼레이션의 종류와 의미를 정의함으로써 코드의 의미를 명확하게 전달하고 개발자의 실수를 방지하기 위해 사용됩니다. 
#### 객체지향 패러다임 관점의 타입
지금까지의 내용을 바탕으로 타입은 다음과 같은 2가지 관점에서 정의할 수 있습니다. 
1. 개념 관점에서 타입이란 공통 특징을 공유하는 대상들의 분류입니다.
2. 프로그래밍 언어 관점에서 타입이란 동일한 오퍼레이션을 적용할 수 있는 인스턴스들의 집합입니다. 

두 정의를 토대로 객체지향 패러다임의 관점에서 조합하면 프로그래밍 언어 관점에서 타입은 호출 가능한 오퍼레이션의 집합을 정의한다는 것입니다. 
객체지향 프로그래밍에서 오퍼레이션은 객체가 수신할 수 있는 메시지를 의미합니다. 
따라서 객체의 타입이란 객체가 수신할 수 있는 메시지의 종류를 정의하는 것입니다. 
객체의 타입은 다른 말로 퍼블릭 인터페이스라고 불리기도 합니다.
### 타입 계층
#### 타입 사이의 포함관계
타입 계층을 구성하는 두 타입 간의 관계에서 더 일반적인 타입을 슈퍼 타입이라고 부르고, 특수한 타입을 서브 타입이라고 부릅니다. 
내연과 외연의 관점에서 일반화와 특수화를 정의해보겠습니다.

객체의 정의를 의미하는 내연 관점에서 일반화란 어떤 타입의 정의를 좀 더 보편적이고 추상적으로 만드는 과정을 말합니다. 
반대로 특수화란 어떤 타입의 정의를 좀 더 구체적이고 문맥 종속적으로 만드는 과정을 말합니다. 

집합을 의미하는 외연의 관점에서 일반적인 타입의 인스턴스 집합은 특수한 타입의 인스턴스 집합을 포함하는 슈퍼셋입니다. 
반대로 특수한 타입의 인스턴스 집합은 일반적인 집합에 포함된 서브셋을 말합니다. 
#### 객체지향 프로그래밍과 타입 계층
일반적인 퍼블릭 인터페이스를 가지는 객체들은 더 특수한 퍼블릭 인터페이스를 가지는 객체들의 슈퍼타입입니다. 

서브타입의 인스턴스 집합은 슈퍼타입의 인스턴스 집합의 부분집합이기에 더 특수한 퍼블릭 인터페이스를 가지는 객체들은 동시에 더 일반적인 인터페이스를 가지는 객체들의 집합에 포함됩니다. 
### 서브클래싱과 서브타이핑
다음은 타입 계층을 구현할 때 지켜야하는 제약 사항을 클래스와 상속 관점에서 살펴보도록 하겠습니다.
#### 언제 상속을 사용해야 하는가?
상속의 올바른 용도는 타입 계층을 구현하는 것입니다. 
다음의 조건을 만족해야  올바르게 타입 계층을 위해 상속을 사용했다 말할 수 있습니다.
##### 상속 관계가 is-a관계를 모델링하는가?
일반적으로 **자식 클래스**는 **부모 클래스**다 라고 말해서 이상하지 않다면 상속을 사용할 후보로 간주할 수 있습니다.
##### 클라이언트 입장에서 부모 클래스의 타입으로 자식 클래스를 사용해도 무방한가?
상속 계층을 사용하는 클라이언트의 입장에서 부모 클래스와 자식 클래스의 차이점을 몰라야 합니다. 
이를 자식 클래스와 부모 클래스 사이의 **행동 호환성**이라고 합니다.
#### is-a관계
is-a관계가 생각처럼 직관적이지만은 않습니다.

두가지 사실에 대해 이야기하겠습니다.
펭귄은 새다.
새는 날수 있다.
이를 토대로 코드로 표현하면 다음과 같습니다. 

```java
public class Bird{
	public void fly(){
		...
	}
}

public class Penguin extends Bird{
	...
} 
```
코드를 통해 알 수 있는 사실은 새는 날수 있고 새인 펭귄은 날수 있다고 주장합니다.
하지만 실제로 펭귄은 새이긴 하지만 날수는 없습니다. 
이 코드를 통해 알 수 있는 것처럼 슈퍼타입과 서브타입의 관계는 is-a의 관계보다 행동 호환성이 더 중요하다는 것을 알 수 있습니다. 

만약 is-a라고 표현할 수 있더라도 상속을 사용할 예비 후보로 생각하고 애플리케이션 안에서 어떤 방식으로 협력되는지 살펴본 후 상속의 적용 여부를 결정하는 것이 더 좋은 방법입니다.

#### 행동 호환성
행동 호환의 여부를 판단하는 기준은 클라이언트 관점입니다. 
클라이언트가 두 타입이 동일하게 행동할 것이라고 기대한다면 두 타입 계층으로 묶을 수 있습니다.
하지만 두 타입이 동일하게 행동하지 않을 것이라고 기대한다면 두 타입을 타입계층으로 묶으면 안됩니다.
 
위의 펭귄은 새다라는 말에 현혹당한 채 상속 계층을 유지할 수 있는 해결 방법은 다음의 3가지가 있습니다.

첫번째 방법은 Penguin의 fly 메서드를 오버라이딩해서 내부 구현을 비워두는 것입니다.
```java
public class Penguin extends Bird{
	...
	@Override
	public void fly(){}
}
```
해당 방법은 어떤 행동도 수행하지 않기에 모든 bird가 날 수 있다는 클라이언트의 기대를 만족하지 못합니다.
따라서 올바른 설계라고 할 수 없습니다.

두번째 방법은 Penguin의 fly 메서드를 오버라이딩한 후 예외를 던지게 하는 것입니다.
```java
public class Penguin extends Bird{
	...
	@Override
	public void fly(){
		throw new UnsupportedOperationException();
	}
}
```
하지만 이방법 역시 fly 메서드를 전송한 결과로 Exception이 발생하는 것은 문제가 있다 봅니다.

세번째 방법은 flyBird 메서드를 수정해서 인자로 전달된 bird타입이 Penguin이 아닐 경우에만 fly메시지를 전송하도록 하는 것입니다.
```java
public void flyBird(Bird bird){
	// 인자로 전달된 모든 bird가 모든 Penguin의 인스턴스가 아닐 경우에만
	// fly() 메시지를 전송한다.
	if(!bird instanceof Penguin){
		bird.fly();
	}
}
```
해당 방법은 새로운 새가 상속 계층에 추가된다면 또 코드를 추가하게 됨으로 개방-폐쇄 원칙을 위반하게 되어 좋은 방법은 아닙니다.

#### 클라이언트의 기대에 따라 계층 분리하기
지금까지 살펴본 것 처럼 행동 호환성을 만족시키지 않은 상속 계층을 그대로 유지한채 클라이언트의 기대를 충족시킬 수 있는 방법을 찾기란 쉽지 않습니다. 
문제를 해결할 수 있는 방법은 클라이언트의 기대에 맞게 상속 계층을 분리하는 것입니다. 

```java
public class Bird{
	...
}

public class FlyingBird extends Bird{
	public void fly(){...}
	...
}

public class Penguin extends Bird{
	...
}
```
다음 코드는 새에는 날 수 없는 새와 날 수 있는 새의 두분류로 구분을 하였고, 그 중 펭귄을 날 수 없는 새로 구분한 것입니다. 

```java
public void flyBird(FlyingBird bird){
	bird.fly();
}
```
이제 flyBird 메서드는 FlyingBird 타입을 이용해 날 수 있는 새만 인자로 전달돼야 한다는 사실을 코드에 명시할 수 있습니다. 
만약 날 수 없는 새와 협력하는 메서드가 존재한다면 파라미터의 타입을 Bird로 선언하면 됩니다. 

이렇게 분리를 통해 나는 새(FlyingBird)도 날지 못하는 펭귄(Penguin)도 Bird에 속하게 되었으며, 혹여나 FlyingBird, Penguin의 타입이 Bird로 변경되더라도 문제가 생기지 않습니다.

이 문제를 해결하는 외 다른 방법은 클라이언트에 따라 인터페이스를 분리하는 방법이 있습니다.
만약 Bird가 날 수 있으면서 걸을 수도 있어야 하고, 펭귄(Penguin)은 오직 걸을 수만 있다고 가정해보겠습니다. 

<img style="width:100%" src="https://github.com/OiKimiO/object/assets/53805469/281ea9b2-c4bd-4fff-8356-659e5989bb79" />
클라이언트에 따라 인터퍼에스를 분리하면 변경에 대한 영향을 더 세밀하게 제어할 수 있게 됩니다. 
대부분의 경우 인터페이스는 클라이언트의 요구가 바뀜에 따라 변경이 됩니다. 
클라이언트에 따라 인터페이스를 분리하면 각 클라언트의 요구가 바뀌더라도 파급 효과를 효과적으로 제어할 수 있게 됩니다. 

이처럼 인터페이스를 클라이언트의 기대에 따라 분리함으로써 변경에 의해 영향을 제어하는 설계 원칙을 인터페이스 분리 원칙이라고 부릅니다.

요점은 요구사항 속에서 클라이언트가 기대하는 행동에 집중하는 것입니다. 
클래스의 이름 사이에 어떤 연관성이 있다는 사실은 아무런 의미가 업습니다. 
두 클래스 사이에 행동이 호환되지 않는다면 올바른 타입 계층이 아니기에 상속을 이용해서는 안됩니다. 

#### 서브클래싱과 서브타이핑
상속을 사용하는 2가지 목적은 **서브클래싱**과 **서브타이핑**입니다.
##### 서브클래싱
다른 클래스의 코드를 재사용할 목적으로 상속을 사용하는 경우를 말합니다. 
자식 클래스와 부모 클래스의 행동이 호환되지 않기에 인스턴스가 부모 클래스의 인스턴스를 대체할 수 없습니다.
**서브클래싱**을 **구현 상속** 또는 **클래스 상속**이라고 부르기도 합니다.
##### 서브타이핑
서브타이핑에서는 자식 클래스와 부모 클래스의 행동이 호환되기에 자식 클래스의 인스턴스가 부모 클래스의 인스턴스를 대체할 수 있습니다. 
이때 부모 클래스는 자식 클래스의 슈퍼타입이 되고 자식 클래스는 부모 클래스의 서브 타입이 이됩니다. 
**서브타이핑**을 **인터페이스 상속**이라고 부르기도 합니다.

**서브 클래싱**은 클래스의 내부 구현 자체를 상속받는 것에 초점을 맞추기에 구현 상속 또는 클래스 상속이라고 불립니다.

서브 타이핑 관계가 유지 되기 위해서는 서브타입이 슈퍼타입이 하는 모든 행동을 동일하게 수행할 수 있어야 합니다. 
즉, 어떤 타입이 다른 타입이 되기 위해서는 **행동 호환성**을 만족시켜야 합니다. 

자식 클래스가 부모 클래스를 대신할 수 있기 위해서는 자식 클래스가 부모 클래스가 사용되는 모든 문맥에서 자식 클래스와 동일하게 행동할 수 있어야 합니다.
그리고 행동 호환성을 만족하는 상속 관계는 부모 클래스를 새로운 자식 클래스로 대체하더라도 시스템이 문제 없이 동작할 것이라는 것을 보장해야 합니다.
다시 말해서 자식 클래스와 부모 클래스 사이의 행동 호환성은 부모 클래스에 대한 자식 클래스의 **대체 가능성**을 포함합니다.
### 리스코프 치환 원칙
리스코프 치환 원칙을 한마디로 정리하면 **서브타입은 그것의 기반 타입에 대해 대체가능해야 한다는 것**으로 클라이언트가 **차이점을 인식하지 못한 채 기반 클래스의 인터페이스를 통해 서브 클래스를 사용할 수 있어야 한다**는 것입니다. 

다음은 리스코프 치환 원칙을 위반하는 전형적인 예입니다. 
대부분의 사람들은 **정사각형은 직사각형이다**라는 이야기를 당연하게 생각합니다. 
하지만 정사각형은 직사각형이 아닐 수 있습니다. 
사실 정사각형과 직사각형의 상속 관계는 리스코프 치환 원칙을 위반하는 고전적인 사례 중 하나입니다. 

```java
public class Rectangle{
	private int x, y, width, height;

	public Rectangle(int x, int y, int width, int height){
		this.x= x;
		this.y= y;
		this.width= width;
		this.height= height; 
	}

	public int getWidth(){
		return width;
	}

	public void setWidth(int width){
		this.width= width;
	}

	public int getHeight(){
		return height;
	}

	public void setHeight(int height){
		this.height= height;
	}

	public int getArea(){
		return width*height;
	}
	
	public void resize(Rectangle rectangle, int width, int height){
		rectangle.setWidth(width);
		rectangle.setHeight(height);
		assert rectangle.getWidth() == width && rectangle.getHeight() == height; 
	}
}

public class Square extends Rectangle{
	public Square(int x, int y, int size){
		super(x, y, size, size);
	} 

	@Override
	public void setWidth(int width){
		super.setWidth(width);
		super.setHeight(width);
	}

	@Override
	public void setHeight(int height){
		super.setWidth(height);
		super.setHeight(height);
	}
}

public TestClass{
	@Test
	void test(){
		Square square = new Square(10, 10, 10);
		square.resize(square, 50 , 100);
	}
}
```
다음의 Rectangle, Square 클래스가 존재합니다.
Rectangle 클래스는 width, height를 각각 설정할 수 있습니다. 
Square 클래스는 width, height의 값을 입력할 때 각각 입력하지 않고 width와 height값을 동시에 입력합니다. 

그리고 Square -> Rectangle를 증명하기 위해 square의 resize메서드를 실행합니다.
그런데 resize 메서드를 살펴보면 직사각형에 대한 내용은 참임을 확인할 수 있습니다.
하지만 resize 메서드를 통해 정사각형임을 입증하는 것에는 어려움이 있습니다. 

그 이유는 Square 클래스는 각각 setWidth, setHeight를 호출할 때 width와 height를 같은 값으로 맞추기때문입니다.
결국 테스트에서 setWidth당시 width, height는 각각 50이였다가 이후 setHeight를 호출할 땐 width, height가 각각 100으로 변경됩니다.

그리고 rectangle.getWidth() == width && rectangle.getHeight() == height의 코드를 현재의 값으로 대입하면 100 == 50 && 100 == 100이 됩니다.

결국 Retangle은 is-a라는 말이 얼마나 우리의 직관에서 벗어날 수 있는지를 잘 보여줍니다.
중요한 것은 클라이언트 관점에서 행동이 호환되는지 여부입니다. 
그리고 행동이 호환될 경우에만 자식 클래스가 부모 클래스 대신 사용될 수 있습니다.
#### 클라이언트와 대체 가능성
리스코프 치환 원칙은 클라이언트와 격리한 채로 본 모델은 의미있게 검증하는 것이 불가능하다는 아주 중요한 결론을 이끕니다. 
어떤 모델의 유효성은 클라이언트의 관점에서만 검증 가능하다는 것입니다.

결국 행동 호환성과 리스코프 치환 원칙에서 한가지만 기억해야 한다면 **대체 가능성을 결정하는 것은 클라이언트**라는 것입니다.
#### is-a 관계 다시 살펴보기
클라이언트 관점에서 자식 클래스의 행동이 부모 클래스의 행동과 호환되지 않고 그로 인해 대체 불가능하다면 어휘적으로 is-a라고 말할 수 있다고 하더라도 그 관계를 is-a관계라고 할 수는 없습니다. 

is-a는 클라이언트 관점에서 is-a일 때만 참입니다.
정사각형은 직사각형인가? 클라이언트가 이 둘을 동일하게 취급할 수 있을 때만 그렇습니다.
is-a관계로 표현된 문장을 볼 때마다 문장 앞에서 **클라이언트 입장에서**라는 말이 빠져 있다고 생각하는 것이 좋습니다
(클라이언트 입장에서) 정사각형은 직사각형이다.

결론적으로 상속이 서브타이핑을 위해 사용될 경우에만 is-a관계가 됩니다.
서브클래싱을 구현하기위해 상속을 사용했다면 is-a관계라고 말할 수 없습니다.
#### 리스코프 치환 원칙은 유연한 설계의 기반이다
클라이언트 입장에서 퍼블릭 인터페이스의 행동 방식이 변경되지 않는다면 클라이언트의 코드를 변경하지 않고도 새로운 자식 클래스와 협력할 수 있게 됩니다. 

8장에서 중복 할인 정책을 구현한기 위해 기존의 DiscoutPolicy 상속 계층에 새로운 자식 클래스인 OverlappedDiscountPolicy를 추가하더라도 클라이언트를 수정할 필요가 없었습니다.

해당 설계는 의존성 역전 원칙, 개방-폐쇄 원칙, 리스코프 치환 원칙이 어우러져 설계를 확장 가능하게 만든 대표적인 예입니다. 
##### 의존성 역전 원칙
구체 클래스인 Movie, OverlappedDiscountPolicy 모두 추상 클래스인 DiscountPolicy에 의존합니다. 
상위 수준의 모듈인 Movie와 하위 수준 모듈인 OverlappedDiscountPolicy는 모두 추상 클래스인 DiscountPolicy에 의존함으로 이 설계는 DIP를 만족합니다. 
##### 리스코프 치환 원칙
DiscountPolicy와 협력하는 Movie의 관점에서 DiscountPolicy대신 OverlappedDiscountPolicy와 협력하더라도 아무런 문제가 없습니다. 
다시 말해서 OverlappedDiscountPolicy는 클라이언트에 대한 영향 없이 DiscountPolicy를 대체할 수 있습니다. 
따라서 이 설계는 LSP를 만족합니다.
##### 개방-폐쇄 원칙
중복 할인 정책이라는 새로운 기능을 추가하기 위해 DiscountPolicy의 자식 클래스인 OverlappedDiscountPolicy를 추가하더라도 Movie에는 영향을 끼치지 않습니다. 
다시 말해서 기능 확장을 하면서 기존 코드를 수정할 필요는 없습니다. 
따라서 이 설계는 OCP를 만족합니다.
### 계약에 의한 설계와 서브타이핑
클라이언트와 서버 사이의 협력을 **의무**와 **이익**으로 구성된 계약의 관점에서 표현하는 것을 **계약에 의한 설계**라고 부릅니다.
계약에 의한 설계는 클라이언트가 정상적으로 메서드를 실행하기 위해 만족시켜야 하는 **사전조건**, 메서드가 실행된 후에 서버가 클라이언트에게 보장해야 하는 **사후조건**, 메서드 실행 전과 실행후에 인스턴스가 만족시켜야 하는 **클래스 불변식**의 세가지 요소로 구성됩니다. 

리스코프 치환원칙과 계약에 의한 설계 사이의 관계는 다음과 같이 한 문장으로 요약할 수 있습니다. 
**서브타입이 리스코프 치환 원칙을 만족시키기 위해서는 클라이언트와 슈퍼타입간에 체결된 계약을 준수해야 한다.**

이해를 돕기 위해 영화 예매 시스템에서 DiscountPolicy와 협력하는 Movie 클래스를 예로 들어보겠습니다. 
```java
public class Moive{
	...
	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
Movie는 DiscountPolicy의 인스턴스에게 calculateDiscountAmount 메시지를 전송하는 클라이언트입니다.
DiscountPolicy는 Movie의 메시지를 수신한 후 할인 가격을 계산해서 반환합니다. 

```java
public abstract class DiscountPolicy{
	public Money calculateDiscountAmount(Screening screening){
		for(DiscountCondition each : conditions){
			if(each.isSatisfiedBy(screening)){
				return getDiscountAmount(screening);
			}
		}
	}

	abstract protected Money getDiscountAmount(Screening screening);
}
```
계약에 의한 설계에 따르면 협력하는 클라이언트와 슈퍼타입의 인스턴스 사이에는 어떤 계약이 맺어져 있습니다. 
클라이언트와 슈퍼타입은 이 계약을 준수할 때만 정상적으로 협력할 수 있습니다. 

지금까지는 Movie와 DiscountPolicy 사이의 계약에 대해서는 크게 상관하지 않았습니다. 
하지만 코드를 살펴보면 직접적으로 언급을 하지 않았을 뿐 암묵적인 사전조건과 사후조건이 존재한다는 사실을 알 수 있습니다. 

먼저 사전조건부터 살펴보겠습니다. 
DiscountPolicy의 calculateDiscountAmount 메서드는 인자로 전달된 screening이 null인지 여부를 확인하지 않습니다. 
하지만 screening에 null이 전달된다면 screening.getMovieFee()가 실행될 때 NullPointerException 예외가 던져질 것입니다. 

그래서 null을 처리하기 위해 단정문(assertion)을 사용해 다음과 같이 표현할 수 있습니다.
```java
assert screening != null && screening.getStartTime().isAfter(LocalDateTime.now()); 
```

또한 청구 되는 요금은 항상 존재하며  0보다 커야함으로 다음과 같이 표현할 수 있습니다.
```java
assert amount != null && amount.isGreaterThanOrEqual(Money.ZERO);
```

다음은 calculateDiscountAmount 메서드에 사전 조건과 사후 조건을 추가한 것입니다. 
사전 조건은 checkPrecondition 메서드로, 사후 조건은 checkPostcondition 메서드로 구현대 있습니다. 
```java
public abstract class DiscountPolicy{
	public Money calculateDiscountAmount(Screening screening){
		checkPrecondition(screening);

		Money amount = Money.ZERO;
		for(DiscountCondition each: conditions){
			if(each.isSatisfiedBy(screening)){
				amount= getDiscountAmount(screening);
				checkPostcondition(amount);
				return amount;
			}
		}

		amount = screening.getMovieFee();
		checkPostcondition(amount);
		return amount;
	}

	protected void checkPrecondition(Screening screening){
		assert screening != null && screening.getStartTime().isAfter
		(LocalDateTime.now()); 
	}

	protected void checkPostcondition(Money amount){
		assert amount != null && amount.isGreaterThanOrEqual(Money.ZERO);
	}

	abstract protected Money getDiscountAmount(Screening screening);
}
```

calculateDiscountAmount 메서드가 정의한 사전 조건을 만족시키는 것은 Movie의 책임입니다.
따라서 Movie는 사전 조건을 위반하는 screening을 전달해서는 안됩니다. 
```java
public class Movie{
	public Money calculateMovieFee(Screening screening){
		if(screening == null || screening.getStartTime().isBefore(LocalDateTime.now())){
		throw new InvalidScreeningException();
		}

		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
#### 서브 타입과 계약
물론 모든 상황이 이렇게 행복한 것 만은 아닙니다. 
계약의 관점에서 상속이 초래하는 가장 큰 문제는 자식 클래스가 부모 클래스의 메서드를 오버라이딩 할 수 있다는 것입니다. 
# 읽고 느낀 점
#### 클라이언트 입장에서 is-a관계
13장을 읽으며 가장 기억에 남는 부분입니다.
is-a관계가 참이기 위해선 클라이언트 입장에서 그 관계를 입증할 수 있어야 한다는 것입니다. 
그리고 is-a관계를 생각하며 가장 먼저 떠올랐던 것은 레거시 프로젝트의 개선이었습니다.
#### 테스트 코드는 클라이언트, 모듈은 is-a관계의 정의
레거시 코드를 만들 땐 테스트 코드가 반드시 필요하다는 글을 읽었습니다.
왜냐하면 테스트 코드의 입력값을 통해 프로세스 입증과 개선 포인트를 찾을 수 있기 때문입니다. 
그리고 이번 장에선 그 말에 대해 조금 더 심도깊게 알 수 있었는데 그 이유는 테스트 코드와 모듈의 관계가 클라이언트 - (is-a관계)로 정의될 수 있다고 봤기 때문입니다. 

Movie - DiscountAmount의 입장을 봐서도 알지만 Movie 입장에서 DiscountAmount의 자식 클래스들과행위 호환성을 입증하기 위해 클라이언트에서 테스트를 진행합니다.

그리고 너무 당연하게도 모든 자식 클래스가 부모 클래스를 대체할 수 있다는 것도 확인할 수 있었습니다. 
#### 행위 호환을 증명하기 위한 테스트 코드
결국 이런 행위 호환을 증명한 것은 Movie 입장에서 DiscountAmount 자식 클래스의 행위를 입력값으로 입증할 수 있었기 때문입니다.
결국 테스트 코드의 존재는 프로세스의 파악도 있지만 어떤 부분에 있어 개선의 여지를 줄 수 있는지를 파악할 수 있다는 것입니다. 
특히 의존성 역전 원칙, 리스코프 치환 원칙, 개방-폐쇄 원칙을 준수하고 있는지를 파악할 수 있으며, 이를 통해 시스템이 얼만큼의 확장성과 안정성을 지키고 있는지를 알 수 있는 기준이 될 수 있을 것이라는 생각이 들었습니다.


