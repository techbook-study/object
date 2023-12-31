# 간략한 내용 정리

# 6 메시지와 인터페이스
- 훌륭한 객체지향은 클래스가 아니라 객체에 지향해야 한다 -> 협력 안에서 객체가 수행하는 책임에 초점을 맞춰야 함

## 6-1 협력과 메시지
### 클라이언트-서버 모델
- 클라이언트 = 메시지를 전송하는 객체
- 서버 = 메시지를 수신하는 객체
- 협력 = 클라이언트가 서버의 서비스를 요청하는 단방향 상호작용
- 메시지를 매개로 하는 요청과 응답의 조합이 두 객체의 협력을 구성함
- 객체는 더 큰 책임을 수행하기 위해 협력해야 하며 이를 가능하게 하는 매개체가 바로 메시지임

### 메시지와 메시지 전송
- 메시지 = 객체들이 협력하기 위해 사용할 수 있는 유일한 의사소통 수단
- 메시지 전송, 메시지 패싱 = 객체가 다른 객체에게 도움으 ㄹ요청하는 것
- 메시지 전송자 = 메시지를 전송하는 객체, 클라이언트
- 메시지 수신자 = 메시지를 수신하는 객체, 서버
- 메시지는 오퍼레이션명(operation name)과 인자(argument)로 구성됨
- 메시지 전송은 메시지 + 메시지 수신자임
- isSatisfiedBy(screening) = 메시지, condition.isSatisfiedBy(screening) = 메시지 전송

### 메시지와 메서드
- 메서드 = 메시지를 수신했을 때 실제로 실행되는 함수 또는 프로시저
- 코드 상에서 동일한 변수에게 동일한 메시지를 전송하더라도 객체의 타입에 따라 실행되는 메서드가 달라질 수 있음
- 메시지와 메서드의 구분은 전송자와 수신자를 느슨하게 결합될 수 있게 만듬

### 퍼블릭 인터페이스와 오퍼레이션
- 퍼블릭 인터페이스 = 객체가 의사소통을 위해 외부에 공개하는 메시지의 집합
- 오퍼레이션 = 퍼블릭 인터페이스에 포함된 메시지, isSatisfiedBy, 구현이 아닌 추상화임
- 메서드 호출 보다는 오퍼레이션 호출이 더 적절함

### 시그니처
- 시그니처 = 오퍼레이션(또는 메서드)의 이름과 파라미터 목록
- 오퍼레이션은 코드 없이 시그니처만 정의한 것이고 메서드는 시그니처에 구현을 더한 것임

## 6-2 인터페이스와 설계 품질
- 좋은 인터페이스는 최소한의 인터페이스(꼭 필요한 오퍼레이션만)와 추상적인 인터페이스(어떻게가 아니라 무엇을)를 만족해야 함
- 메시지가 객체를 선택하게 만듬

### 디미터 법칙
- 협력하는 객체의 내부 구조에 대한 결합으로 인해 발생하는 설계 문제를 해결하기 위해 제안된 원칙
- 객체의 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한하라는 것
- 클래스 내부의 메서드가 아래의 조건을 만족하는 인스턴스에만 메시지를 전송하도록 프로그래밍 해야함
    - this 객체
    - 메서드의 매개변수
    - this의 속성
    - this의 속성인 컬렉션 요소
    - 메서드 내에서 생성된 지역 객체
- 부끄럼타는 코드(shy code) = 불필요한 어떤 것도 다른 객체에게 보여주지 않으며 다른 객체의 구현에 의존하지 않는 코드
- `screening.getMovie().getDiscountConditions()` 이러한 코드를 기차 충돌(train wreck)이라 부르며 클래스의 내부 구현이 외부로 노출됐을 때 나타나는 전형적인 형태임

### 묻지말고 시켜라
- 메시지 전송자는 메시지 수신자의 상태를 기반으로 결정을 내린 후 메시지 수신자의 상태를 바꿔서는 안됨
- 상태를 바꾸는 것은 메시지 수신자가 담당해야할 책임임
- 인터페이스는 객체가 어떻게 하는지가 아니라 무엇을 하는지를 서술해야 함

### 의도를 드러내는 인터페이스
- 메서드가 어떻게 작업을 수행하는지를 나타내도록 이름을 지어야 함
- 어떻게가 아니라 무엇을 하는지를 드러내도록 이름을 지어야 함
- 메서드가 어떻게 수행하느냐가 아니라 무엇을 하느냐에 초점을 맞추면 동일한 작업을 수행하는 메서드들을 하나의 타입 계층으로 묶을 수 있는 가능성이 커짐

### 함께 모으기
- 디미터 법칙을 위반하는 설계는 인터페이스와 구현의 분리 원칙을 위반함
- 디미터 법칙과 묻지말고 시켜라 스타일을 따르면 자연스럽게 자율적인 객체로 구성된 유연한 협력을 얻게 됨
- 결합도는 낮아지고 응집도는 높아짐
- 오퍼레이션의 이름은 협력이라는 문맥을 반영해야 함

## 6-3 원칙의 함정
- 설계 = 트레이드오프의 산물

### 디미터 법칙은 하나의 도트(.)를 강제하는 규칙이 아니다
- 디미터 법칙은 결합도와 관련된 것이며 이 결합도가 문제가 되는 것은 객체의 내부 구조가 외부로 노출되는 경우로 한정됨
- 기차 충돌처럼 보이는 코드라도 객체의 내부 구현에 대한 어던 정보도 외부로 노출하지 않는다면 그것은 디미터 법칙을 준수한 것임

### 결합도와 응집도의 충돌
- 일반적으로 어떤 객체의 상태를 물어본 후 반한된 상태를 기반으로 결정을 내리고 그 결정에 따라 객체의 상태를 변경하는 코드는 묻지말고 시켜라 스타일로 변경해야 됨
- 디미터 법칙과 묻지말고 시켜러 원칙을 준수한다고 해서 항상 긍정적인 결과로 귀결되지는 않음
- 위임 메서드를 추가하면 같은 퍼블릭 인터페이스 안에 어울리지 않은 오퍼레이션들이 공존하게 되고 이는 상관 없는 책임들을 떠안게 되기 떄문에 응집도가 낮아질 수 있음
- 객체인 경우 디미터 법칙을 따르는 것이 좋지만 자료 구조라면 당연히 내부를 노출해야 하므로 디미터 법칙을 적용할 필요가 없음

## 6-4 명령-쿼리 분리 원칙
- 루틴(routine) = 어떤 절차를 묶어 호출 가능하도록 이름을 부여한 기능 모듈, 프로시저(procedure)와 함수(function)로 구분됨
- 프로시저 = 정해진 절차에 따라 내부의 상태를 변경하는 루틴의 한 종류, 부수효과를 발생시킬 수 있지만 값을 반환할 수 없음
- 함수 = 어떤 절차에 따라 필요한 값을 계산해서 반환하는 루틴의 한 종류, 값을 반환할 수 있지만 부수효과를 발생시킬 수 없음
- 명령과 쿼리는 객체의 인터페이스 측면에서 프소지와 함수를 부르는 또 다른 이름임
- 명령(command) = 객체의 상태를 수정하는 오퍼레이션, 프로시저
- 쿼리(query) = 객체와 관련된 정보를 반환하는 오퍼레이션, 함수
- 어떤 오퍼레이션도 명령인 동시에 쿼리여서는 안됨 -> 질문이 답변을 수정해서는 안됨

### 반복 일정의 명령과 쿼리 분리하기
- 쿼리안에 명령이 함께 있으면 실행 결과를 예측하기 어려움 -> 버그를 양산함
- 메서드가 부수효과를 가지는지를 확인하기 위해 메서드가 반환 값을 가지는지 여부만 확인하여 분석가능함
- 명령과 쿼리를 분리하면 코드는 예층 가능하고 이해하기 쉬우며 디버깅이 용이한 동시에 유지보수가 수월해짐

### 명령-쿼리 분리와 참조 투명성
- 컴퓨터의 세계와 수학의 세계를 나누는 가장 큰 특징은 부수효과(side effect)의 존재 유무임
- 프로그램은 대입문 등을 통해 부수효과가 발생됨
- 참조 투명성 = 어떤 표현식 e가 있을 때 e의 값으로 e가 나타나는 모든 위치를 교체하더라도 결과가 달라지지 않는 특성
- 어떤 값이 불변하다는 말은 부수효과가 발생하지 않는다는 말과 동일함
- 객체지향 패러다임이 객체의 상태변경이라는 부수효과를 기반으로 하기 때문에 참조 투명성은 예외에 가까움 -> 명령-쿼리 분리 원칙을 사용하여 이 균열을 조금이나마 줄일 수 있음

### 책임에 초점을 맞춰라
- 메시지를 먼저 선택하고 그 후에 메시지를 처리할 객체를 선택해라
- 훌륭한 메시지를 얻기 위한 출발점은 책임 주도 설계 원칙을 따르는 것임
- 계약에 의한 설계(Design By Contract) = 협력을 위해 클라이언트와 서버가 준수해야 하는 제약을 코드 상에 명시적으로 표현하고 강제할 수 있는 방법

# 읽고 느낀 점
- 매번 비슷하면서도 다른듯한 용어들에 대해 다시 한번 복습하는 느낌이었다
- 누군가와 얘기할 때는 사전에 이러한 의미는 같은 의미로 받아들이는게 좋겠다라는 등의 협의가 필요할 것 같다
- 인터페이스의 품질을 결정할 수 있는 디미터 법칙, 묻지말고 시켜라, 의미를 드러내는 인터페이스, 함께 모으기 등을 알아가는 것 같다. -> 꼭 개발할때 써보자
- 항상 설계는 트레이드오프의 산물로 무언가를 무조건 신뢰하지도 무언가를 무조건 배척하는 자세는 좋지 않은 것 같다.
- 뭐든 적절한 상황 속에서 사용할 수 있도록 경험을 늘려야 겠다.
- 명령-쿼리라는 언어를 cqrs 패턴에서 접했는데 단순히 cud는 커맨드로 r은 쿼리로 정도로 알고 있는데 훨씬 본질을 알 수 있었던 것같다.