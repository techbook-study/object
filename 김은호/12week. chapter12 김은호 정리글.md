# 간략한 내용 정리
## 다형성
### 다형성
다형성이라는 단어는 그리스어에서 **많은**을 의미하는 poly와 **형태**를 의미하는 morph의 합성어로 **많은 형태를 가질 수 있는 능력**을 의미합니다.
객체 지향 프로그래밍에서 사용되는 다형성은 다음과 같이 구분됩니다.
#### 유니버설(Universal) 다형성
##### 매개변수(Parametic) 다형성
매개변수 다형성은 제네릭 프로그래밍과 관련이 높은데 클래스의 인스턴스 변수나 메서드의 매개변수 타입을 임의의 타입으로 선언한 후 사용하는 시점에 구체적인 타입으로 지정하는 방식을 말합니다. 
예를 들어, 자바의 List 인터페이스는 컬렉션에 보관할 요소의 타입을 임의의 타입 T로 지정하고 있으며 실제 인스턴스를 생성하는 시점에 T를 구체적인 타입으로 지정할 수 있게 합니다.
##### 포함(Inclusion) 다형성
메시지가 동일하더라도 수신한 객체의 타입에 따라 실제로 수행되는 행동이 달라지는 능력을 의미합니다.
보통 특별한 언급이 없이 다형성이라 할 경우엔 포함 다형성을 의미하는 것이 일반적입니다.

아래 코드는 포함 다형성의 전형적인 예를 잘 보여줍니다. 
Movie 클래스는 discountPolicy에게 calculateDiscountAmount메시지를 전송하지만 실제로 실행되는 메서드는 메시지를 수신한 객체 타입에 따라 달라집니다.

```java
public class Movie{
	private DiscountPolicy discountPolicy;

	public Money calculateMovieFee(Screening screening){
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```
#### 임시(Ad Hoc) 다형성
##### 오버로딩(Overloading) 다형성
일반적으로 하나의 클래스 안에서 동일한 이름의 메서드가 존재하는 경우를 말합니다. 

```java
public class Money{
	public Money plus(Money amount){...}
	public Money plus(BigDecimal amount){...}
	public Money plus(long amount){...}
}
```
이렇듯 메서드 오버로딩을 사용해 유사한 작업을 수행하는 메서등의 이름을 통일할 수 있기에 기억해야 하는 이름의 수를 극적으로 줄일 수 있습니다.
##### 강제(Coercion) 다형성
언어가 지원하는 자동적인 타입변환이나 사용자가 직접 구현한 타입 변환을 이용해 동일한 연산자를 다양한 타입에 사용할 수 있는 방식을 말합니다. 
예를 들어, 자바에서 이항연산자인 **+** 는 피연산자가 모두 정수일 경우 덧셈 연산자가 되지만 문자일 경우 연결 연산자로 사용됩니다. 

### 상속의 양면성 
객체 지향 패러다임의 근간을 이루는 아이디어는 데이터와 행동을 객체라고 불리는 하나의 실행단위 안으로 통합하는 것입니다.

상속 역시 부모 클래스에서 정의한 모든 데이터를 자식 클래스의 인스턴스에 자동으로 포함시킬 수 있는데, 이를 데이터 관점의 상속이라 할 수 있습니다.
또한 부모 클래스에서 정의한 일부 메서드 역시 자동으로 클래스에 포함시킬 수 있는데 이것을 행동 관점에서의 상속이라고 할 수 있습니다.

단순히 데이터와 행동의 관점에서 바라본 상속은 부모 클래스에서 정의한 데이터와 행동을 자식 클래스에서 자동적으로 공유할 수 있는 매커니즘으로 보일 것입니다.

하지만 상속의 목적은 코드 재사용이 아닌 프로그램을 구성하는 개념들을 기반으로 다형성을 가능하게 하는 타입 계층을 구축하기 위함입니다. 
이에  상속의 매커니즘을 이해하고, 잘못된 사용을 방지하기 위한 개념과 코드를 소개합니다.
#### 상속을 사용한 강의 평가
##### Lecture 클래스 살펴보기 
이번 장에서는 수강생들의 성적을 계산하는 예제 프로그램을 구현합니다. 
프로그램은 다음과 같은 형식으로 전체 수강생들의 성적 통계를 출력합니다. 
```text
Pass:3 Fail:2, A:1 B:1 C:1 D:0 F:2
```
출력은 두 부분으로 구성됩니다. 앞 부분의 **Pass:3 Fail:2**는 강의를 이수한 학생의 수와 낙제한 학생의 수를 나타냅니다. 
뒷부분의 **A:1 B:1 C:1 D:0 F:2**는 등급별로 학생들의 분포 현황을 나타낸 것입니다.

먼저 Lecture 클래스의 구현을 살펴보도록 하겠습니다. 
Lecture 클래스는 과목명(title)과 학생들의 성적을 보관할 리스트(scores)를 인스턴스 변수로 가집니다. 
인스턴스 변수인 pass는 이수 여부를 판단할 기준 점수를 저장합니다. 만약 학생 성적이 pass에 저장된 값보다 크거나 같을 경우엔 강의를 이수한 것으로 판단합니다. 
... 

```java
public class Lecture{
	private int pass;
	private String title;
	private List<Integer> scores = new ArrayList<>();

	public Lecture(String title, int pass, List<Integer> scores){
		this.title = title;
		this.pass = pass;
		this.scores = scores;
	}

	public double average(){
		return scores.stream()
					 .mapToInt(Integer::intValue)
					 .average().orElse(0);
	}

	public List<Integer> getScores(){
		return Collections.unmidifiableList(scores);
	}

	public String evaluate(){
		return String.format("Pass:%d Fail:%d", passCount(), failCount());
	}

	public int passCount(){
		return scores.stream().filter(score -> score >= pass).count();
	}

	public int failCount(){
		return scores.size() - passCount();
	}
}
```

다음은 이수 기준 70점인 객체 지향 프로그래밍 과목의 수강생 5명에 대한 통계를 구하는 코드를 나타낸것입니다. 
```java
Lecture lecture = new Lecture("객체지향 프로그래밍",
							 70,
							 Arrays.asList(81,95,75,50,45));
String evaluation = lecture.evaluate(); // 결과 -> Pass:3 Fail:2
```
##### 상속을 이용해 Lecture 클래스 재사용하기
Lecture 클래스는 새로운 기능을 구현하는데 피료한 대부분의 데이터와 메서드를 포함하고 있습니다. 
따라서 Lecture 클래스를 상속받으면 새로운 기능을 쉽고 빠르게 추가할 수 있을 것입니다. 

원하는 기능은 Lecture의 출력 결과에 등급별 통계를 추가하는 것이므로 클래스의 이름으로는 GradeLecture가 적절할 것으로 보입니다. 
GradeLecture 클래스에는 Grade 인스턴스들을 리스트로 보관하는 인스턴스 변수 grades를 추가합니다. 
```java
public class GradeLecture extends Lecture{
	private List<Grade> grades;

	public GradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores){
		super(name, pass, scores);
		this.grades = grades;
	}
}
```

Grade 클래스는 등급의 이름과 각 등급 범위를 정의하는 최소 성적과 최대 성적을 인스턴스 변수로 포함합니다. 
include 메서드는 수강생의 성적이 등급에 포함되는 지를 검사합니다. 
```java
public class Grade{
	private String name;
	private int upper, lower;

	private Grade(String name, int upper, int lower){
		this.name = name;
		this.upper = upper;
		this.lower = lower;
	}

	public String getName(){
		return name;
	}

	public boolean isName(String name){
		return this.name.equals(name);
	}

	public boolean include(int score){
		return score >= lower && score <= upper;
	}
}
```

GradeLecture 클래스에 학생들의 이수 여부와 동급별 통계를 함께 반환하도록 evaluate 메서드를 재정의해보겠습니다. 

```java
public class GradeLecture extends Lecture{
	...
	
	@Override
	public String evaluate(){
		return super.evaluate() + ", " + gradeStatistics();
	}

	private String gradesStatistics(){
		return grades.stream()
					 .map(grade -> format(grade))
					 .collect(joining(" "));
	}

	private String format(Grade grade){
		return String.format("%s:%d", grade.getName(), gradeCount(grade));
	}

	private long gradeCount(Grade grade){
		return getScores().stream()
						  .filter(grade::include)
						  .count();
	}
}
```
위 코드 처럼 자식 클래스안에 상속받은 메서드와 동일한 시그니처의 메서드를 재정의해서 부모 클래스의 구현을 새로운 구현으로 대체하는 것을 **메서드 오버라이딩**이라고 부릅니다. 

```java
Lecture lecture = new GradeLecture("객체지향 프로그래밍",
									70,
									Arrays.asList(new Grade("A",100,95),
												  new Grade("B",94,80),
												  new Grade("C",79,70),
												  new Grade("D",69,50),
												  new Grade("F",49,0)),
									Arrays.asList(81, 95, 75, 50, 45));

// 결과 => "Pass:3 Fail:2, A:1 B:1 C:1 D:1 F:1"
lecture.evaluate();
```

자식 클래스에 부모클래스에 없던 새로운 메서드를 추가하는 것도 가능합니다. 
예를 들어 다음과 같이 등급별 평균 성적을 구하는 average 메서드를 추가할 수 있습니다.

```java
public class GradeLecture extends Lecture{
	... 
	public double average(String gradeName){
		return grades.stream()
					 .filter(each -> each.isName(gradeName))
					 .findFirst()
					 .map(this::gradeAverage)
					 .orElse(0d);
	}

	private double gradeAverage(Grade grade){
		return getScores().stream()
						  .filter(grade::include)
						  .mapToInt(Integer::intValue)
						  .average()
						  .orElse(0);
	}
}
```
해당 코드는 Lecture의 average와 똑같은 이름의 메서드가 존재합니다.
하지만 각 시그니처가 다르기에 다른 메서드로 볼 수 있고, 이를 **메서드 오버로딩**이라 부릅니다.

#### 데이터 관점의 상속 
다음과 같이 Lecture의 인스턴스를 생성했다고 가정해보겠습니다.
```java
Lecture lecture = new Lecture("객체지향 프로그래밍",
							  70,
							  Arrays.asList(81, 95, 75, 50, 45));
```
이를 메모리 공간에 할당했다고 가정하면 다음과 같다 할 수 있습니다.
그림12.2

다음은 GradeLecture의 인스턴스를 생성했다고 가정해보겠습니다. 
```java
Lecture lecture = new GradeLecture("객체지향 프로그래밍",
									70,
									Arrays.asList(new Grade("A",100,95),
												  new Grade("B",94,80),
												  new Grade("C",79,70),
												  new Grade("D",69,50),
												  new Grade("F",49,0)),
									Arrays.asList(81, 95, 75, 50, 45));

```
이를 메모리 공간으로 할당했다고 하면 다음과 같다고 할 수 있습니다.
그림 12.4

그림을 통해서도 알 수 있지만 데이터 관점에서 상속은 자식 클래스의 인스턴스안에 부모 클래스를 포함하는 것을 볼 수 있습니다. 
따라서 자식 클래스이 인스턴스는 자동으로 부모 클래스에서 정의한 모든 인스턴스 변수를 내부에 포함하게 됩니다. 
#### 행동 관점의 상속
행동 관점에서의 상속은 부모 클래스가 정의한 일부 메서드를 자식 클래스의 메서드로 포함시키는 것을 말합니다. 
보통 부모 클래스에 정의된 어떤 메서드가 자식 클래스에 포함될 지는 접근 제어자에 따라 다르지만 부모 클래스의 모든 퍼블릭 메서드는 자식 클래스의 퍼블릭 인터페이스에 포함됩니다. 

그리고 자식 클래스는 부모 클래스의 퍼블릭 인터페이스를 사용할 수 있는데 그 이유는 런타임에 시스템이 자식 클래스에 정의되지 않은 메서드가 있을 경우 부모 클래스 안에서 탐색하기 때문입니다. 

객체의 경우에 서로 다른 상태를 저장할 수 있도록 각 인스턴스별 독립적인 메모리를 할당받습니다. 
하지만 메서드의 경우엔 동일한 클래스의 인스턴스끼리 공유가 가능하기에 클래스는 한번만 메모리에 로드하고 각 인스턴스별로 클래스를 가리키는 포인터를 갖게 하는 것이 경제적입니다. 

그림 12.5는 두개의 Lecture 인스턴스를 생성한 후 메모리 상태를 개념적으로 표현한 것입니다. 그림에서 오른쪽에 위치한 사각형들은 메모리에 로드된 클래스를 표현합니다. 
인스턴스는 2개가 생성됐지만 클래스는 단 하나만 메모리에 로드가 되었습니다. 
각 객체는 자신의 클래스인 Lecture의 위치를 가르키는 class라는 이름의 포인터를 가지며 이 포인터를 이용해 자신의 클래스 정보에 접근할 수 있습니다. 

Lecture 클래스가 자신의 부모 클래스인 Object의 위치를 가리키는 parent라는 이름의 포인터를 가지는데, 이를 이용해 클래스의 상속 계층을 따라 부모 클래스의 정의로 이동하는 것이 가능해집니다.

부모 클래스에 정의된 메서드를 사용하는 것은 순서는 먼저 자식 클래스안에서 적절한 메서드가 있는지 탐색하고 만약 해당 메서드가 없을 경우 부모 메서드의 포인터로 찾아가 메서드가 존재하는지를 검색합니다.

### 업캐스팅과 동적 바인딩
#### 같은 메시지, 다른 메서드
```java
public class Professor{
	private String name;
	private Lecture lecture;

	public Professor(String name, Lecture lecture){
		this.name = name;
		this.lecture = lecture;
	}

	public String compileStatistics(){
		return String.format("[%s] %s - Avg: %.1f", name, lecture.evaluate(), lecture.average());
	}
}
```

다음은 다익스트라 교수가 강의하는 알고리즘 과목의 성적 통계를 계산하는 코드입니다.
```java
Professor professor = new Professor("다익스트라",
								    new Lecture("알고리즘", 
											    70, 
											    Arrays.asList(81, 95, 75, 50, 45)));

// 결과 => [다익스트라] Pass:3 Fail:2 - Avg: 69.2
String statistics = professor.compileStatistics();
```

```java
Professor professor = new Professor("다익스트라",
								    new GradeLecture("알고리즘",
													 70,
									Arrays.asList(new Grade("A",100,95),
												  new Grade("B",94,80),
												  new Grade("C",79,70),
												  new Grade("D",69,50),
												  new Grade("F",49,0)),
									Arrays.asList(81, 95, 75, 50, 45)));

// 결과 => [다익스트라] Pass:3 Fail:2, A:1 B:1 C:1 D:1 F:1 - Avg: 69.2
String statistics = professor.compileStatistics();
```
위의 코드는 동일한 수신자에게 동일한 메시지를 전송하는 동안 동일한 코드를 이용해 서로 다른 메서드를 실행합니다.
그리고 그것이 가능한 이유는 **업캐스팅**과 **동적 메서드 탐색**이라는 기반 메커니즘이 존재하기 때문입니다. 

**업캐스팅**은 서로 다른 클래스의 인스턴스를 동일한 타입에 할당하는 것을 가능하게 만들어 줍니다. 
그래서 부모 클래스에 대해 작성된 코드를 수정하지 않고도 자식 클래스에 적용할 수 있게 만들어 줍니다. 

**동적 메서드 탐색**은 부모 클래스의 타입에 대해 메시지를 전송하더라도 실행 시에는 실제 클래스를 기반으로 실행될 메서드가 선택되게 해줍니다. 
그래서 코드를 변경하지 않고도 실행되는 메서드를 변경할 수 있습니다.
#### 업캐스팅
상속을 이용하면 부모 클래스의 퍼블릭 인터페이스가 자식 클래스의 퍼블릭 인터페이스에 합쳐지기에 부모 클래스의 인스턴스에게 전송할 수 있는 메시지를 자식 클래스의 인스턴스에 전송할 수 있습니다. 

부모 클래스의 인스턴스 대신 자식 클래스의 인스턴스를 사용하더라도 메시지를 처리하는 데는 아무런 문제가 없으며, 컴파일러는 명시적인 타입 변환 없이도 자식 클래스가 부모 클래스를 대체할 수 있게 허용합니다. 
이를 **업케스팅**이라고 합니다.

반대로 부모 클래스에서 자식 클래스 타입으로 변환하는 것도 가능합니다. 이는 **다운 캐스팅**이라합니다.
#### 동적 바인딩
객체 지향 언어에서는 메시지를 수신했을 때 실행될 메서드가 런타임에 결정됩니다. 
예를 들어, foo.bar() 라는 코드를 읽는 것만으로는 실행되는 bar가 어떤 클래스의 메서드인지를 판단하기 어렵습니다. 
foo가 가르키는 객체가 실제로 어떤 클래스의 인스턴스인지를 알아야 하고 bar 메서드가 해당 클래스의 상속 계층의 어디에 위치하는지를 알아야 합니다. 
이처럼 실행될 메서드를 런타임에 결정하는 방식을 **동적 바인딩** 또는 **지연 바인딩**이라고 부릅니다.

### 동적 메서드 탐색과 다형성
객체지향 시스템은 다음 규칙에 따라 실행할 메서드를 선택합니다. 
- 메시지를 수신한 객체는 먼저 자신을 생성한 클래스에 적합한 메서드가 존재하는지 검사합니다. 존재한다면 메서드를 실행하고 탐색을 종료합니다. 
- 메서드를 찾지 못했다면 부모 클래스에서 메서드 탐색을 계속합니다. 이 과정은 적합한 메서드를 찾을 때까지 상속 계층을 따라 올라가며 계속됩니다. 
- 상속 계층의 가장 최상위 클래스에 이르렀지만 메서드를 발견하지 못한 경우 예외를 발생시키며 탐색을 중단합니다. 

메시지 탐색과 관련해 이해해하는 변수 중 하나는 self 참조(self reference)입니다.
객체가 메시지를 수신하면 컴파일러는 self 참조라는 임시 변수를 자동으로 생성한 후 메시지를 수신한 객체를 가르키도록 설정합니다.
동적 메서드 탐색은 self가 가리키는 객체의 클래스에서 시작해서 상속 계층의 역방향으로 이뤄지며 메서드 탐색이 종료되는 순간 self참조는 자동으로 소멸됩니다. 

메서드 탐색은 자식 클래스에서 부모 클래스 방향으로 진행됩니다. 
따라서 항상 자식 클래스의 메서드가 보무 클래스 메서드보다 먼저 탐색되기에 자식 클래스에 선언된 메서드가 부모 클래스의 메서드 보다 더 높은 우선순위를 가지게 됩니다.

지금까지 설명을 종합하면 동적 메서드 탐색은 2가지 원리로 구성된다는 것을 알 수 있습니다.
1. **자동적인 메시지 위임**
    자식 클래스는 자신이 이해할 수 없는 메시지를 받았을 경우 상속 클래스인 부모 클래스에 위임하게 됩니다. 
2. **동적인 문맥 사용**
     메시지를 수신했을 때 실제로 어떤 메서드를 실행할지를 결정하는 것은 컴파일 시점이 아닌 실행시점에 이뤄지며, 메서드를 탐색하는 경로는 self 참조를 이용해 결정합니다.
#### 자동적인 메시지 위임
상속을 이용할 경우 프로그래머가 메시지 위임과 관련된 코드를 명시적으로 작성할 필요가 없습니다. 메서지는 상속 계층을 따라 부모 클래스에게 자동으로 위임이 됩니다. 
이런 관점에서 상속 계층을 정의하는 것은 메서드 탐색 경로를 정의하는 것과 동일합니다. 

**메서드 오버라이딩**은 자식 클래스의 메서드가 동일한 시그니처를 가진 부모 클래스의 메서드 보다 먼저 탐색되기에 발생되는 현상입니다. 

**메서드 오버로딩**은 이름만 같고 시그니처가 완전히 동일하지 않는 메서드들이기에 상속 계층에 걸쳐 공존할 수 있습니다.
#### 동적인 문맥
메시지 전송 코드만으로는 어떤 클래스의 어떤 메시지가 실행되는지 알 수 없고 메시지를 수신한 객체가 무엇이냐에 따라 메서드 탐색을 위한 문맥이 동적으로 바뀐다는 사실을 알 수 있었습니다. 
그리고 이 동적인 문맥을 결정하는 것이 바로 메시지를 수신한 객체를 가리키는 self 참조입니다. 

동일한 코드라고 해도 self 참조가 가르키는 객체가 무엇인지에 따라 메서드 탐색을 위한 상속 계층의 범위가 동적으로 변경됩니다.
따라서 self 참조가 가르키는 객체의 타입을 변경함으로써 객체가 실행될 문맥을 동적으로 변경할 수 있습니다. 

그런데 만약 self 참조를 상속으로 표현하면 조금은 복잡해질 수 있습니다. 
<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/ac62998d-3639-4433-8668-75c753ccf73a" />
예를 들어 셀프 참조가 향하는 메서드가 stats()라고 했을 때 일단 자식 클래스인 GradeLecture에선 메서드를 찾을 수 없어 부모 클래스인 Lecture로 향합니다.
그리고 Lectrue의 stats()를 실행했을 때 this.getEvaluationMethod의 메시지 전송을 self참조에게 지시합니다. 
self 참조는 다시 GradeLecture 클래스에서 self참조가 전송한 getEvaluationMethod 메시지를 찾게되고 결국 GradeLecture 클래스에서 getEvaluationMethod를 실행하게 됩니다. 

이처럼 self 전송이 깊은 상속 계층과 계층 중간중간에 함정처럼 숨겨져 있는 메서드 오버라이딩과 만나면 극단적으로 이해하기 어려운 코드가 만들어 질수도 있습니다.
#### 이해할 수 없는 메시지
이해할 수 없는 메시지를 처리하는 방법은 프로그래밍 언어가 정적 타입에 속하는지 동적 타입에 속하는지에 따라 달라지게 됩니다. 
##### 정적 타입 언어와 이해할 수 없는 메시지
정적 타입 언어에서는 이해할 수 없는 메시지가 있을 경우 상속 계층을 따라 메시지를 처리할 수 있는지 확인하고 없으면 컴파일 에러를 발생시켜 처리할 수 없는 메시지라는 것을 프로그래머에게 전달합니다.
##### 동적 타입 언어와 이해할 수 없는 메시지
동적 타입 언어에서 역시 메시지를 수신한 객체의 부모 클래스의 방향으로 메서드를 탐색합니다.
단, 컴파일 단계가 존재하지 않아 실제로 코드를 실행하기 전에는 메시지 처리 가능 여부를 판단할 수 없습니다.  

동적 타입 언어는 존재하지 않는 메서드에 대한 에러 처리를 응답하는 메서드를 구현할 수도 있습니다. 
이렇듯 동적 타입 언어는 객체의 자율성을 줄 수 있어 정적 언어보단 좀 더 객체지향적이긴 합니다. 
하지만 이러한 동적인 특성과 유연한 코드라는 특징때문에 코드를 이해하고 수정하기 어렵게 만들기도 합니다.
#### self 대 super
self 참조의 가장 큰 특징은 동적이라는 점입니다. 
self 참조는 메시지를 수신한 객체의 클래스에 따라 메서드 탐색을 위한 문맥을 실행 시점에 결정합니다. 
self의 이런 특성과 대비해서 언급할 만한 가치가 있는 것이 바로 super 참조(super reference) 입니다.

<img style="width:100%;" src="https://github.com/OiKimiO/object/assets/53805469/92069c9b-7fe7-47be-add1-2647f568c6bb" />

```java
public class FormattedGradeLecture extends GradeLecture{
	public FormattedGradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores){
		super(name, pass, grades, scores);
	}

	public String formatAverage(){
		return String.format("Avg: %1.1f", super.average());
	}
}
```
위 코드가 실행되면 먼저 self 참조는 FormattedGradeLecture를 실행합니다. 
실행을 하며 formatAverage() 메서드를 실행하면 부모 클래스에서 average()가 있는지 찾습니다.
하지만 GradeLecture에는 average() 메서드가 존재하지 않습니다.
결국 super 참조는 Lecture에서 average() 메시지를 전송하게 됩니다. 

# 읽고 느낀 점
#### 상속의 복잡함 
상속을 통한 가장 큰 이점은 코드를 재사용하고 부모-자식 클래스간의 기능을 명확히 정의할 수 있는 것이라 생각합니다.
하지만 가장 큰 단점은 코드 파악에 있어 복잡하다는 단점 또한 가지고 있습니다.
예를 들어 상속을 Object를 포함해서 5번의 상속을 한다면? 5번째 자식이 1번째 부모의 함수를 실행하게 된다면? ... 생각만 해도 엄청 파악이 힘들어진다는 단점을 가지고 있다 생각합니다. 
#### 유연함 vs 가독성
그렇기에 상속은 유연함도 있지만 가독성을 생각했을 땐 정말 필요한 것인지를 판단할 필요하단 생각을 했습니다.
혹여나 유연함과 가독성을 따져야 하는 상황이라면 상속, 합성, 다형성을 어떤 순간에 사용하면 좋을지.. 한번 나눠보는 것도 좋아 보입니다.