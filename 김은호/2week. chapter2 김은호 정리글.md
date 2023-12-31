1. 2장 README를 만들어라

2. 커밋 기록 방식을 변경하라 
- 커밋 메시지는 순서대로 변수, 생성자, 메서드로 구성됩니다.
- 변수, 메서드 등에서 타입은 반드시 적어줍니다.
- 특정 파일을 생성 = ~만들어라 
- 기존 클래스에 메서드를 추가 = ~기능을 추가
- 기존 클래스에서 메서드를 삭제 = ~기능을 삭제
- public = 공용, private = 구현, constructor = 생성자를 의미합니다.

3. 현금을 만들어라
- 공용 = 0원(final Money)이 있습니다.
- 구현 = 금액(BigDecimal)가 있습니다.
- 생성자 = 현금에 금액을 입력받습니다.
- 공용 = 금액에 해당하는 원을 가져오세요: Money wons(long), Money wons(double)
- 공용 = 금액을 더해서 가져오세요: Money plus(Money)
- 공용 = 금액을 빼서 가져오세요: Money minus(Money)
- 공용 = 금액에 비율을 곱하세요: Money times(double)
- 공용 = 다른 금액보다 작은지 확인하세요: boolean isLessThan(Money)
- 공용 = 다른 금액보다 큰지 확인하세요: boolean isGreaterThanOrEqual(Money)

4. 상영을 만들어라
- 구현 = 영화(Movie), 순번(int), 상영 시작시간(LocalDateTime)이 있습니다.
- 생성자 = 상영은 영화, 순번, 상영시작시간을 입력받습니다.
- 공용 = 시작시간을 가져오세요: LocalDateTime getStartTime
- 공용 = 순번과 일치하는지 확인하세요: boolean isSequence(int)
- 공용 = 영화관람료를 가져오세요: Money getMovieFee
- 영화(Movie)를 만들어라
	- 관람료를 가져오세요: getFee -> null

5. 영화 예매 기능 구현 > 상영에 예매기능을 추가
- 공용 = 관객과 관객수만큼 예매하라: Reservation reserve(Customer, int)
	- 구현 = 예약 생성자만 입력합니다.
- 구현 = 관객수만큼 관람료를 계산하라: Money calculateFee(int)
	- 구현 = 총 관람객 수 만큼 영화 관람료를 계산해주세요   
- 예약(Reservation), 관람객(Customer)을 만들어라

6. 영화 예매 기능 구현 > 예약에 입력정보를 추가
- 구현 = 관람객(Customer), 상영(Screening), 관람료(Money),  관람객수(int)가 있습니다.
- 생성자 = 예약은 관람객, 상영, 현금, 관람객 수를 입력받습니다.
- 상영에 관객과 관객수만큼 예매하라의 rerturn 값을 수정합니다.

7. 할인 요금 계산 기능 구현 > 영화를 만들어라
- 구현 = 제목(String), 상영시간(Duration), 관람료(Money), 할인정책(DiscountPolicy)가 있습니다. 
- 생성자 = 영화의 제목, 상영시간, 관람료, 할인정책을 입력받습니다.
- 공용 = 관람료를 가져오세요: Money getFee
- 공용 = 상영되는 영화의 관람료를 계산해주세요: Money calculateMovieFee(Screening)
	- 구현 = 영화 관람료에서 할인 금액만큼 빼주세요
- 할인 정책(DiscountPolicy)을 만들어라
	- 공용 = 상영에 해당하는 할인금액을 계산하세요: calculateDiscountAmount(Screening) -> null 
- 상영(Screening)에 관객수만큼 관람료를 계산하는 과정에서 관람료는 할인 정책이 적용된 영화의 관람료(calculateMovieFee)를 적용합니다.   

8. 할인 요금 계산 기능 구현 > 공통 할인 정책에 기능을 추가
- 구현 = 할인 조건들(List<DiscountCondition>)이 있습니다. 
- 생성자 = 할인 조건들을 입력받습니다
- Template Method 패턴을 이용해 할인 금액을 계산합니다.
- 공용 = 상영에 해당하는 할인금액을 계산합니다: Money calculateDiscountAmount(Screening)
	- 구현 = 상영되는 영화가 입력된 할인 조건들 중 하나를 만족한다면 할인 금액을 가져옵니다. 하지만 만족되는 값이 없을 경우 0을 가져옵니다.
- 공용 추상 = 상영에 해당하는 할인금액을 가져옵니다: Money getDiscountAmount(Screening)
- 할인 조건(DiscountCondition) 인터페이스를 만들어라
	- 공용 = 상영에 할인 조건이 충족되는지 확인하세요: boolean isSatisfiedBy(Screening) 
- 현금(Money)의 0원, ~원을 가져오세요(won)을  static으로 처리합니다. 


10. 할인 조건 인터페이스 구현 > 순번 조건을 만들어라 
- 구현 = 순번(sequence)이 있습니다.
- 생성자  = 할인을 받기 위한 순번조건을 받습니다. 
- 공용 = 상영의 순번과 일치하는지 확인하세요: boolean isSatisfiedBy(Screening)

11. 할인 조건 인터페이스 구현 > 기간 조건을 만들어라
- 구현 = 주의일수(DayOfWeek), 할인 시작시간(LocalTime), 할인 종료시간(LocalTime)이 있습니다.
- 생성자 = 주의일수, 시작시간, 종료시간을 입력받습니다. 
- 공용 = 상영 기간과 일치하는지 확인하세요: boolean isSatisfiedBy(Screening)
	- 구현 = 상영기간이 해당 요일이면서, 할인 시작시간과 할인 종료시간의 범위에 상영 시간이 있는지 확인합니다. 

12. 공통 할인 정책 세부 구현 > 정률 할인 정책을 만들어라
- 구현 = 할인 금액(int)이 있습니다.
- 생성자 = 할인 금액, 할인 조건(부모)을 입력 받습니다.
- 공용 = 상영에 대한 할인 금액을 가져오세요: Money getDiscountAmount(Screening)
	- 구현 = 할인금액을 가져오세요.

13. 공통 할인 정책 세부 구현 > 비율 할인 정책을 만들어라
- 구현 = 할인 금액 비율(double)이 있습니다.
- 생성자 = 할인 금액, 할인 조건(부모)을 입력 받습니다.
- 공용 = 상영에 대한 할인 금액을 가져오세요: Money getDiscountAmount(Screening)
	- 구현 = 상영 영화 관람료에 할인 비율을 곱한 할인 금액을 가져오세요.

14. 영화에서 할인 요금이 없는 상영을 예외처리로 추가
- 기존 상영되는 영화의 관람료를 계산(calculateMovieFee)하는 로직에서 할인 정책이 없는 영화는 예외처리로 영화 관람료를 리턴하도록 추가합니다. 
- 추가 후: 할인 정책의 예외를 영화에서 처리하는 방식은 일관성이 무너져 좋은 방식이 아닙니다. 

15. 공통 할인 정책 세부 구현 > 할인 없는 정책을 만들어라
- 공용 = 상영에 할인 금액을 가져오세요: Money getDiscountAmount(Screening)
	- 구현 = 0원을 리턴하세요.
- 변경 후: 공통 할인 정책을 적용하기에 기존 로직의 일관성이 유지됩니다.

16. 할인 없는 정책의 개념적 혼란을 제거하라 
- 기존 공통 할인 정책에선 할인 조건은 반드시 입력되어야 하기에 추상 클래스를 인터페이스로 변경해 할인 금액 계산만 담당하도록 합니다.
	- 공용 = 할인 금액을 계산하세요: Money calculateDiscountAmount(Screening)
- 할인 조건을 입력받는 것은 기본 할인 정책이라는 추상 클래스를 만들어 기존의 할인 정책들을 관리합니다.
	- 기본 할인 정책(DefaultDiscountPolicy)을 만들어라
	- 정률 할인 정책과 비율 할인 정책은 기본 할인 정책을 상속받습니다. 

17. 실행시점에 할인 정책을 변경할 수 있도록 변경하라
- 공용 = 할인정책을 변경하세요: changeDiscountPolicy(DiscountPolicy)
- 변경 후
	- 상속을 통한 관계는 강결합으로 연결되어 변경에 취약해지고, 실행 시점에 객체의 종류를 변경하기 어려워 집니다.  
	- 이에 인터페이스에 정의된 메세지만으로 코드를 재사용하는 합성을 통해 좀 더 유연하게 기능을 사용할 수 있습니다. 

읽고 느낀점
이번 장의 핵심은 객체간의 협력과 추상화라고 생각합니다. 
시스템의 흐름을 안다는 것은 객체간의 협력을 안다는 것이고, 객체들간의 협력을 안다는 것은 각 객체에 필요한 상태와 행동을 정의할 수 있다는 것입니다.
그리고 정의된 각 객체들간의 상태와 행동을 보며 중복된 객체들간의 추상화가 가능해진다는 것을 알 수 있게 됩니다. 
물론 추상화의 과정속에서 되려 Template Method Pattern과 같이 부모와 자식간의 결합도가 높아지는 경우도 생길 수 있습니다.
하지만 추상화가 객체간의 협력이 명확한 상황에서 모든 것을 고려했을 때 결정한 사안이라면 일부 결합도가 있는 부분은 추후 리팩터링을 통해 문제를 해결할 수 있다고 생각합니다. 