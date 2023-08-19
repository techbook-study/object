# 간략한 내용 정리
## 객체 분해
사람의 기억은 장기 기억과 단기 기억으로 나눌 수 있습니다. 
장기 기억은 사람들이 오랫동안 기억한 내용을 포함하고 있지만 단기 기억은 기억할 수 있는 내용의 제한이 있습니다. 
장기 기억이 이전에 기록된 많은 기록을 가지고 있지만 그 기억을 곧바로 사용할 순 없고 단기 기억을 통해 장기 기억을 사용할 수 있게 됩니다. 

그리고 효율적으로 단기 기억에서 최대한 효율적으로 장기 기억으로 접근을 위해서는 기존의 내용을 잘 추상화시키는 작업이 필요합니다.
여기서 말하는 추상화란 문재 해결에 필요한 핵심만 남기는 작업을 말합니다. 

조금 더 들어가 추상화를 한 이후에도 다뤄야할 문제가 크다면 그 문제를 조금 더 작은 단위로 나누는 분해라는 작업이 필요합니다. 
분해의 목적은 큰 문제를 인지의 과부화 문제 없이 단기 기억안에서 한 번에 처리할 수 있는 규모로 나누는 것을 말합니다.

이렇듯 복잡성이라는 작업이 필요한 곳에는 추상화와 분해라는 개념이 적용되었는데 이 개념이 적용된 영역 중 하나가 소프트웨어 개발 영역입니다. 

### 프로시저 추상화와 데이터 추상화
프로그래밍 언어를 통해 표현되는 추상화의 발전은 다양한 프로그래밍 패러다임의 탄생으로 이어졌습니다. 

프로그램 패러다임이란 적절한 추상화의 윤곽을 따라 시스템을 어떤 식으로 나눌 것인지를 결정하는 원칙과 방법의 집합을 말합니다. 
그리고 현대 설계 방법의 중요한 영향을 미치는 프로그램 패러다임은 **프로시저 추상화**나 **데이터 추상화**를 중심으로 시스템의 분해 방법을 설명합니다. 

여기서 말하는 **프로시저 추상화**는 소프트웨어가 무엇을 해야 하는 지를 추상화하는 것을 말하고, **데이터 추상화**는 소프트웨어가 무엇을 알아야 하는 지를 추상화하는 것을 말합니다.

시스템을 분해하는 방법을 결정하려면 먼저 **프로시저 추상화를 중심으로 할 것**인지, **데이터 추상화를 중심**으로 할 것인지를 결정해야합니다. 
**프로시저 중심으로 시스템을 분해**하기로 결정했다면 **기능 분해**의 길로 들어선 것인데, 이는 **알고리즘 분해**로 불리기도 합니다. 

**데이터 추상화를 중심으로 시스템 분해**를 결정했다면 하나는 데이터를 중심으로 타입을 추상화하는 **추상 데이터 타입**이 있고, 다른 하나는 데이터를 중심으로 프로시저를 추상화하는 **객체 지향**이 있습니다.

이전 장을 거쳐오며 객체지향 패러다임을 역할과 책임을 수행하는 자율적인 객체들의 협력 공동체를 구성하는 것이라 배웠는데, 여기서 **협력하는 공동체를 구성하도록 객체들로 나누는 과정**을 **객체지향 패러다임에서의 분해**를 의미한다고 볼 수 있습니다. 

그렇다면 객체지향이 전통적인 분해 방법에 비해 효과적이라고 말하는 이유는 무엇일까요? 다음은 왜 객체 지향 분해 방법을 이용해야 하는 지를 설명해보고자 합니다. 

### 프로시저 추상화와 기능 분해 
#### 메인 함수로서의 시스템
기능은 오랜시간 시스템을 분해하기 위한 기준으로 사용되었으며, 이를 알고리즘 분리(기능 분해)라고 합니다. 
추상화의 단위는 프로시저이며, 시스템은 프로시저 단위로 분해됩니다.

프로시저는 유사한 기능을 모아둔 재사용 가능한 추상화 단위이지만 효과적인 정보은닉 체계를 구축하는데는 한계가 있습니다. 
프로시저 중심의 기능 분해 관점에서 시스템은 입력값을 계산해서 반환하는 수학 함수와 동일합니다. 그렇기에 시스템은 필요한 더 작은 작업으로 분해할 수 있는 하나의 커다란 메인 함수로 봤습니다. 

이런 전통적인 기능 분해 방법을 하향식 접근법을 따릅니다. 이는 최상위 기능을 정의하고 하위엔 최상위 기능을 조금더 구체화시키는 과정을 통해 기능을 분해하는 방법을 말합니다.
다음은 이런 하향식 접근법을 통한 기능 분해 방식을 급여 관리 시스템으로 설명해보고자 합니다.
#### 급여 관리 시스템
하향식 접근에 따라 기능을 분해하기위해 가장 먼저 최상위 문장을 서술하겠습니다.

> 직원의 급여를 계산한다. 

그리고 다음은 좀 더 세분화된 구체화 하기 위해 직원 이름과 소득 세율을 입력받을 것인데, 직원 이름은 프로시저로 입력받고, 소득 세율은 사용자로 부터 직접 입력 받는 것으로 합니다. 

> 직원의 급여를 계산한다. 
> 	사용자로부터 소득세율을 입력받는다.
> 	직원의 급여를 계산한다.
> 	양식에 맞게 결과를 출력한다. 

다음은 조금 더 구체화시킬 수 있는 지를 확인합니다. 먼저 직원의 급여를 계산하기 위해선 소득세율뿐 아니라 직원의 기본급 정보가 필요합니다. 그래서 직원의 목록과 개별 직원에 대한 기본급 데이터를 시스템 내부에 보관하기로 결정했습니다 .
마지막으로 급여 계산 결과는 "이름:{직원명}, 급여: {계산된 금액}" 형식으로 스크린에 출력하기로 결정했습니다.

> 직원의 급여를 계산한다. 
> 	사용자로부터 소득세율을 입력받는다.
> 		"세율을 입력하세요: " 라는 문장을 화면에 출력한다. 
> 		키보드를 통해 세율을 입력받는다.
> 		
> 	직원의 급여를 계산한다.
> 		전역 변수에 저장된 직원의 기본급 정보를 얻는다. 
> 		급여를 계산한다.
> 		
> 	양식에 맞게 결과를 출력한다.
> 		"이름: {직원명}, 급여: {계산된 금액}" 형식에 따라 출력 문자열을 생성한다.

지금과 같은 기능 분해는 중심에는 기능이 우선된다는 것을 알 수 있으며, 기능이 우선되고 데이터는 그 뒤를 따르고 있다는 것을 알 수 있습니다 .

하지만 지금과 같은 하향식 기능 분해 방식은 문제점을 가지고 있는데 다음은 어떤 문제점을 가지는지 살펴보고자 합니다.

#### 급여 관리 시스템 구현
먼저 문제를 살펴보기 앞서 Ruby라는 언어를 통해 급여 관리시스템을 구현 해보고자합니다.(흠.. 그냥 자바로 하지 왜 굳이 루비했을까요.. ㅎㅎ)

> 직원의 급여를 계산한다. 

```ruby 
def main(name)
end
```
최상위 함수는 다음과 같이 표현할 수 있습니다. 

> 직원의 급여를 계산한다. 
> 	사용자로부터 소득세율을 입력받는다.
> 	직원의 급여를 계산한다.
> 	양식에 맞게 결과를 출력한다. 

```ruby 
def main(name)
	taxRate = getTaxRate()
	pay = calculatePayFor(name, taxRate)
	puts(describeResult(name, pay))
end
```
메인 함수 내 코드는 직원의 급여를 계산한다. 이하의 내용을 세부적으로 구현한 것입니다. 

> 직원의 급여를 계산한다. 
> 	사용자로부터 소득세율을 입력받는다.
> 		"세율을 입력하세요: " 라는 문장을 화면에 출력한다. 
> 		키보드를 통해 세율을 입력받는다.

```ruby 
def getTaxRate()
	print("세율을 입력하세요: ")
	return gets().chomp().to_f()
end
```

직원의 급여를 계산한다.
> 직원의 급여를 계산한다.
> 	사용자로부터 소득세율을 입력받는다.
> 		전역 변수에 저장된 직원의 기본급 정보를 얻는다. 
> 		급여를 계산한다.

```ruby 
$employees = ["직원A", "직원B", "직원C"]
$basePays = [400, 300, 250]

def calculatePayFor(name, taxRate)
	index = $employees.index(name)
	basePay = $basePays[index]
	return basePay - (basePay * taxRate)
end
```
해당 내용은 직원의 이름과 월급에 해당하는 배열을 만들고 월급을 계산하는 코드를 작성합니다. 매월 직원들이 받는 월급은 "기본급 - (기본급 * 소득세율)"입니다. 

> 직원의 급여를 계산한다. 
> 	양식에 맞게 결과를 출력한다.
> 		"이름: {직원명}, 급여: {계산된 금액}" 형식에 따라 출력 문자열을 생성한다.

```ruby
def describeResult(name, pay)
	return "이름: #{name}, 급여: #{pay}"
end
```
만약 이름이 직원C인 직원의 급여를 계산하려면 다음과 같이 프로시저를 호출합니다. 

```ruby
main("직원C")
```

P225 하향식 기능 분해 트리
이처럼 하향식 기능 분해를 통해 논리적이고 체계적인 시스템을 개발할 수 있습니다. 하지만 우리의 세계는 그리 체계적이지도 이상적이징도 않기에 문제가 발생하게 됩니다. 

#### 하향식 기능 분해의 문제점
##### 하나의 메인 함수라는 비현실적인 아이디어
현대적인 시스템은 동등한 수준의 다양한 기능으로 정의됩니다. 하지만 하향식 기능 분해은 메인 함수를 하나로 모든 기능을 정의하려 하기에 현대적인 시스템과는 맞지 않다는 것을 알 수 있습니다.

##### 메인 함수의 빈번한 재설계
현재 급여 시스템은 직원의 급여를 계산하는 기능을 메인으로 합니다. 
그런데 여기서 직원의 급여를 계산하는 작업 이외에 직원의 기본급의 합을 구하는 작업을 진행한다고 가정해보겠습니다. 
이렇게 되는 경우 기존의 메인 함수 내에서 작업이 처리되면서 기존의 시스템에 문제가 발생할 수도 있는 문제의 여지를 남기게 됩니다. 

예를 들어 코드로 보여드리면 다음과 같다고 할 수 있습니다. 
```ruby
def main(operation, args={})
	case(operation)
	when :pay then calculatePayFor(args[:name])
	when :basePays then sumOfBasePays()
end

def sumOfBasePays()
	result = 0
	for basePay in $basePays
		result += basePay
	end
	puts(result)
end

...
```
해당 코드에서 직원의 기본급을 구하기 위해선 main(:basePays)를 호출하면 되고, 기본급의 총합을 구하기위해선 main(:pay, name:"직원A")를 호출합니다. 

그리고 이 코드를 보기만 해도 모든 코드가 main을 향하고 있다는 것을 알 수 있고 결론적으로는 새로운 기능 추가가 예기치 않은 문제를 발생시킬 수 있고, 이는 기존 코드를 빈번히 수정한다는 것에 있어 수정으로 인한 버그 발생 확률을 높이게 됩니다.

##### 비즈니스 로직과 사용자 인터페이스의 결합
현재의 급여 관리 시스템은 비즈니스 로직과 사용자 인터페이스가 결합되어 있습니다. 
코드 상으로 확인했을 때 main함수는 사용자 인터페이스를 sumOfBasePays, calculatePayFor함수는 비즈니스 로직을 구현합니다. 

그런데 여기서 문제는 사용자 인터페이스는 변경이 잦다는 것이고, 비즈니스 로직은 변경이 적다는 것때문에 만약 하나가 변경이 생긴다면 다른 하나도 시스템에 영향을 받게 된다는 것입니다.
여기서 비즈니스 로직의 변경은 사용자 인터페이스으로 인해 계속해서 안정성이 떨어진다는 문제에 직면하게 됩니다. 

##### 성급하게 결정된 실행 순서
하향식으로 기능을 분해하는 과정은 하나의 함수를 더 작은 함수로 분해하고, 분해된 함수들의 실행 순서를 결정하는 작업으로 요약할 수 있습니다. 
이것은 설계를 시작함에 있어 설계 시점부터 무엇(what)을 해야하는 지가 아니라 어떻게(how) 동작해야 하는지에 집중하도록 만듭니다.

하향식 기능 분해는 메인에서부터 시작해 기능을 구체적으로 구분하기 시작하는데 결정적으로 구체적인 내용을 어떻게 구성할지를 고민하며 어떻게에 집중하게 됩니다. 

결국 중앙에서 부터 정의한 기능의 문맥에 종속적일 수 밖에 없게 되고 이는 변경에 취약한 코드를 만들게 됩니다. 

> 직원의 급여를 계산한다. 
> 	사용자로부터 소득세율을 입력받는다.
> 		"세율을 입력하세요: " 라는 문장을 화면에 출력한다. 
> 		키보드를 통해 세율을 입력받는다.

예를 들어 다음의 내용을 확인하면 직원은 급여를 계산한다는 큰 틀에서 사용자로부터 소득세율을 입력받고 그것을 키보드를 통해 세율을 입력받는다는 과정으로 끝을 냅니다. 

여기서 사용자로부터 소득세율을 입력받는다는 직원의 급여를 계산한다라는 문맥에 종속적이며, 해당 기능에 대한 문맥을 정의하고 있어 다른 기능으로의 사용을 어렵게합니다.

결국 각 기능들은 메인으로 부터의 결합도가 높아지는 문제가 발생합니다.
##### 데이터 변경으로 인하 파급효과
하향식 기능 분해의 가장 큰 문제점은 어떤 데이터를 어떤 함수가 사용하고 있는지를 추적하기 어렵다는 것입니다. 따라서 데이터 변경으로 인해 어떤 함수가 영향을 받을지 예상하기 어려워 집니다.

문제에 대한 부분을 정의하기 위해 급여 관리 시스템에 새로운 기능을 추가해보도록 하겠습니다. 급여 관리 시스템에 정규직 직원 이외에 시간제 직원의 급여도 계산해달라는 요청입니다.
해당 요청을 처리하기 위해 다음의 코드를 추가했습니다.

```ruby
$employees = ["직원A", "직원B", "직원C", "아르바이트D", "아르바이트E", "아르바이트F"]
$basePays = [400, 300, 250, 1, 1, 1.5]
$hourlys = [false, false, false, true, true, true]
$timeCards = [0, 0, 0, 120, 120, 120]

... 

def calculatePay(name)
	taxRate = getTaxRate()
	if(hourly?(name)) then
		pay = calculateHourlyPayFor(name, taxRate)
	else
		pay = calculatePayFor(name, taxRate)
	end 
	puts(describeResult(name, pay))
end

def hourly?(name)
	return $hourlys[$employees.index(name)]
end

def calculateHourlyPayFor(name, taxRate)
	index = $employees.index(name)
	basePay = $basePays[index] * $timeCards[index]
	return basePay - (basePay * taxRate)
end

def sumOfBasePays()
	result = 0
	for basePay in $basePays
		result += basePay
	end
	puts(result)
end
```
해당 코드에선 $hourlys와 $timeCards를 변수가 추가되어 이 직원이 정직원인지 시간제 직원인지를 판단할 수 있는 배열이 있습니다.

그리고 calculatePay에서는 각 급여를 계산하는데 먼저 시간제인지를 확인하는 hourly함수를 통해 해당 직원이 정직원이라면 calculatePayFor 메서드를 시간제라면 calculateHourlyPayFor를 통해 급여를 계산합니다. 

이렇게 하면 완벽히 모든 작업이 끝난 것 같지만 문제가 발생하게 되는데 이는 직원들의 급여 총합을 계산하는 sumOfBasePays메서드에서 문제가 발생합니다. 
현재 sumOfBasePays 메서드는 정직원만의 급여 합을 계산해야 하지만 정직원 이외에도 시간제 근무자들의 급여합도 포함해 계산을 하게됩니다.

그래서 문제를 해결하기 위해 다음과 같이 코드를 변경합니다. 

```ruby
def sumOfBasePays()
	result = 0
	
	for name in $employees
		if(not hourly?(name)) then
			result += $basePay[$employees.index(name)]
		end
	end 
	
	puts(result)
end
```


어찌저찌 문제를 해결했지만 만약 이 프로그램이 크고 복잡한 시스템이었다면 해당 기능에 대한 수정이 무척 어려웠을 것입니다. 

결국 위와 같은 문제를 해결하기위해선 영향을 받는 부분과 영향을 받지 않는 부분을 구분해 잘 정의된 퍼블릭 인터페이스를 만들 필요가 있습니다. 

#### 언제 하향식 분해가 유용한가?
하향식 분해는 이미 해결된 알고리즘을 문서화하고 서술하는데 훌륭한 기법입니다. 하지만 실제로 동작하는 커다란 소프트웨어를 설계하는데는 적합한 방법이라 할순 없습니다.

### 모듈
#### 정보 은닉과 모듈
앞서 설명한 것 처럼 시스템의 변경을 관리하는 기본적인 전략은 함께 변경되는 부분을 하나의 구현 단위로 묶고 퍼블릭 인터페이스를 통해서만 접근하도록 만드는 것입니다.
즉 기능을 기반으로 시스템을 분해하는 것이 아니라 변경의 방향을 맞춰 시스템을 분해하는 것입니다. 

정보은닉은 시스템을 모듈 단위로 분리할 때 사용하는 방식인데, 이는 시스템에서 상대적을 잘 변경될 가능성이 있는 것을 뒤로 감추고, 쉽게 변경되지 않을 퍼블릭 인터페이스를 외부에 제공하는 방식을 말합니다. 

시스템을 모듈 단위로 분해할 땐 2가지 비밀을 감춰야 합니다. 
복잡성: 모듈이 너무 복잡한 경우엔 사용하기가 힘들기에, 외부의 모듈을 추상화할 수 있는 간단한 인터페이스를 제공해 모듈의 복잡도를 낮춥니다. 
변경 가능성: 변경 가능한 설계를 외부에 노출하지 않는 것이 좋습니다. 

```ruby
module Employees
	$employees = ["직원A", "직원B", "직원C", "아르바이트D", "아르바이트E", "아르바이트F"]
	$basePays = [400, 300, 250, 1, 1, 1.5]
	$hourlys = [false, false, false, true, true, true]
	$timeCards = [0, 0, 0, 120, 120, 120]

	def Employees.calculatePay(name, taxRate)
		if(hourly?(name)) then
			pay = Employees.calculateHourlyPayFor(name, taxRate)
		else
			pay = Employees.calculatePayFor(name, taxRate)
		end 
	end
	
	def Employees.hourly?(name)
		return $hourlys[$employees.index(name)]
	end
	
	def Employees.calculateHourlyPayFor(name, taxRate)
		index = $employees.index(name)
		basePay = $basePays[index] * $timeCards[index]
		return basePay - (basePay * taxRate)
	end

	def Employees.calculatePayFor(name, taxRate)
		return basePay - (basePay * taxRate)
	end
	
	def Employees.sumOfBasePays()
		result = 0
		
		for name in $employees
			if(not Employees.hourly?(name)) then
				result += $basePay[$employees.index(name)]
			end
		end 
		
		puts(result)
	end
end
```
다음은 전체 직원에 대한 처리를 Employees모듈로 캡슐화한 결과를 나타낸 것입니다. 
지금까지 전연벽수 였던 $employees, $basePays, $hourlys, $timeCards가 Employees라는 모듈 내부로 숨겨져 있습니다. 
이제는 모듈 외부에서는 직원의 정보를 관리하는 데이터에 직접 접근할 수 없고 오직 Employees모듈이 제공하는 인터페이스를 통해서만 내부 변수를 조작할 수 있게 됩니다. 

#### 모듈의 장점과 한계
Employees예제를 통해 알 수 있는 모듈의 장점은 다음과 같습니다. 
##### 모듈 내부의 변수가 변경되더라도 모듈 내부에만 영향을 미칩니다.
##### 비지니스 로직과 사용자 인터페이스에 대한 관심사를 분리합니다. 
##### 전역 변수와 전역 함수를 제거함으로서 네임스페이스 오염을 방지합니다. 

모듈의 장점은 감춰야할 데이터를 결정하고 필요한 함수를 결정하는 방식을 통한 추상화를 통해 응집도를 높였다는 장점을 가지고 있습니다. 

하지만 모듈은 변경을 관리하기위해 구현된 기법이다보니 추상화 관점에서 한계를 명확히 가지고 있습니다. 모듈의 가장 큰 단점은 인스턴스의 개념을 제공하지 않는다는 것인데, 이는 전체 직원에 대한 정보의 추상화는 가능하지만 개별 직원에 대한 추상화는 어렵다는 단점을 가집니다. 
그래서 이런 단점을 보안하기 위해 등장한 개념이 바로 추상 데이터 타입입니다. 

### 데이터 추상화와 추상 데이터 타입
#### 추상 데이터 타입 
기능 분해의 시대엔 사용되던 주된 추상화는 프로시저 추상화이긴 하지만 프로시저 추상화만으로는 프로그램의 표현력을 향상시키는데 한계가 있었습니다. 

바바라 리스코프는 추상화의 한계를 인지하고 대안을 탐색한 선각자 중 한명이고, 데이터 추상화의 개념을 제안한 사람 중 한명입니다.

리스코프가 말한 추상화를 위해선 다음의 특성을 위한 프로그래밍 언어의 지원이 필요합니다.
1. 타입 정의를 선언할 수 있어야 합니다. 
2. 타입의 인스턴스를 다루기 위해 사용할 수 있는 오퍼레이션의 집합을 정의할 수 있어야 합니다.
3. 제공된 오퍼레이션을 통해서만 조작할 수 있도록 데이터를 외부로부터 보호할 수 있어야 합니다. 
4. 타입에 대해 여러개의 인스턴스를 생성할 수 있어야 합니다. 

루비도 추상 데이터 타입을 흉내 낼수는 있지만 Struct 구성요소를 제공합니다. 이를 이용해 이전까지 사용한 급여 관리 시스템을 표현하면 다음과 같습니다. 

```ruby
$employees = [
	Employee.new("직원A", 400, false, 0)
	Employee.new("직원A", 300, false, 0)
	Employee.new("직원C", 250, false, 0)
	Employee.new("아르바이트D", 1, true, 120)
	Employee.new("아르바이트E", 1, true, 120)
	Employee.new("아르바이트F", 1, true, 120)
]

def calculatePay(name)
	taxRate = getTaxRate()
	for each in $employees
		if(each.name == name) then employee = each; break end
	end

	pay = employee.calculatePay(taxRate)
	puts(describeResult(name, pay))
end

def sumOfBasePays()
	result = 0
	
	for each in $employees
		result += each.monthlyBasePay()
	end 
	
	puts(result)
end

Employee = Struct.new(:name, :basePay, :hourly, :timeCard) do
	def calculatePay(taxRate){
		if(hourly) then
			return calculateHourlyPay(taxRate)
		end
		return calculateSalariedPay(taxRate)
	}	

	def monthlyBasePay()
		if(hourly) then return 0 end
		return basePay
	end
	
	private
		def calculateHourlyPay(taxRate)
			return (basePay * timeCard) - (basePay * timeCard) * taxRate
		end
	
		def calculateSalariedPay(taxRate)
			return (basePay * timeCard) - (basePay * taxRate)
		end
	end
end
```
ruby의 struct를 이용해 :name, :basePay, :hourly, :timeCard를 입력받습니다. 해당 방식은 모듈 방식에서 직원을 따로 지정했던 방식보다 조금 더 간단히 직원을 표현할 수 있다는 장점이 있습니다.  
이렇게 만든 방식이 이전보다 조금 더 Employee를 좀 더 현실세계의 개념에 맞게 사용했으며, 데이터에 대한 과정을 설계의 표면으로 끌어올리긴 했습니다.
하지만 아직도 여전히 데이터와 기능을 분리하는 절차적인 설계에 틀에 갇혀 있는 한계점이 존재합니다.  
### 클래스
#### 클래스는 추상 데이터 타입인가?
추상 데이터 타입과 클래스는 동일하지 않습니다. 가장 핵심적인 차이는 클래스는 상속과 다형성을 지원하는데 비해 추상 데이터 타입은 지원하지 못한다는 점입니다.
상속과 다형성을 지원하는 객체지향 프로그래밍과 구분하기 위해 상속과 다형성을 지원하지 않는 추상 데이터 타입 기반의 프로그래밍 패러다임을 객체 기반 프로그래밍이라고 부르기도 합니다.

#### 추상 데이터 타입에서 클래스로 변경하기
```ruby
$employees = [
	Employee.new("직원A", 400)
	Employee.new("직원A", 300)
	Employee.new("직원C", 250)
	Employee.new("아르바이트D", 1, 120)
	Employee.new("아르바이트E", 1, 120)
	Employee.new("아르바이트F", 1, 120)
]

class Employee
	attr_reader :name, :basePay

	def initialize(name, basePay)
		@name= name
		@basePay= basePay
	end

	def calculatePay(taxRate)
		raise NotImplemenedError
	end

	def monthlyBasePay()
		raise NotImplementedError
	end
end

class SalariedEmployee < Employee
	def initialize(name, basePay)
		super(name, basePay)
	end

	def calculatePay(taxRate)
		return basePay - (basePay * taxRate)
	end

	def monthlyBasePay()
		return basePay
	end
end

class HourlyEmployee < Employee
	attr_reader :timeCard
	
	def initialize(name, basePay)
		super(name, basePay)
		@timeCard= timeCard
	end

	def calculatePay(taxRate)
		return (basePay * timeCard) - (basePay * timeCard) * taxRate
	end

	def monthlyBasePay()
		return 0
	end
end
```
루비에서의 상속 관계는 `자식 클래스 < 부모 클래스`로 표현할 수 있습니다. 
그리고 클래스로 변경하면서 두드러지는 점은 상속을 통해 $employees들의 데이터가 정규직이냐 아르바이트냐에 따라 필요한 데이터만 전달하고 있다는 것입니다. 

#### 변경을 기준으로 선택하라
설계는 변경을 위해서 존재하는 것이고, 이런 변경을 잘 받아드리긴 위해서 기존의 코드엔 영향을 미치지 않으면서 코드를 변경 할 수 있어야 합니다.
이렇게 코드를 만드는 원칙을 개방-폐쇄원칙이라 합니다. 

하지만 모든 설계가 타입 추상화가 중점이 될 필요는 없습니다. 상황에 따라 타입이 추상화가 될수도 있고 데이터 타입이 추상화가 될 수도 있기 때문입니다.

# 읽고 느낀 점
이번장의 마지막은 다음과 같이 언급합니다. 
>타입 계층과 다형성은 협력이라는 문맥안에서 책임을 수행하는 방법에 관해 고민한 결과물이어야 하며 그 자체가 목적이 되어서는 안됩니다.

결국 협력을 중심으로 개발을 하지만 상황에 따라 객체지향적일 수도 그렇지않을 수도 있다는 내용을 내포하는 것 같습니다. 

그런데 참 아이러니한게 객체 지향적으로 프로그래밍 하기위한 책을 읽고 있는데, 뭔가 읽을 수록 객체 지향적 원칙과 방법이 완벽한 정답은 아니라는 느낌이 많이 드는 것 같습니다.
우리가 객체 지향적으로 개발을 하는 목적이 **설계의 변경**을 위해서인 것이고, 결국 목적에 따라선 달라질 수 있다는 것을 많이 느낍니다. 
그리고 이러한 목적에 따른 다양한 적용법은 다양한 사례를 경험하고, 그 사례를 분석하고, 협력을 기반으로 구축하는 과정을 통해 하나씩하나씩 쌓일 수 있을 것이란 생각이 듭니다. 