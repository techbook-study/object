# 객체, 설계

## 01. 티켓 판매 애플리케이션 구현하기
### 매출이 오르고 있는 상황에서 홍보 겸 작은 이벤트를 기획한다. 추첨을 통하여 무료 관람권 초대장 발송!
- 당첨 여부 확인
- 무료 관람권 티켓으로 교환 / 티켓 구매

### 당첨자에게 발송되는 초대장 구현
- 클래스 초대장 Invitation 
- 초대일자 when

### 공연 관람을 위해 티켓을 소지
- 클래스 티켓 Ticket
- 요금 fee

### 관람객들이 가방에 가지고 있는 세가지들 (초대장, 현금, 티켓)
- 클래스 가방 Bag
- 인스턴스 현금 amount
- 인스턴스 초대장 invitation
- 인스턴스 티켓 ticket
- 메소드 초대장 소지 여부 hasInvitation
- 메소드 티켓 소지 여부 hasTicket
- 메소드 현금 증가 plusAmount
- 메소드 현금 감소 minusAmount
- 메소드 티켓 교환 setTicket
- 생성자 현금 / 현금, 초대장

### 가방 소유한 관람객
- 클래스 관객 Audience
- 가방 bag

### 티켓 교환/판매, 판매 금액 매표소
- 클래스 매표소 TicketOffice
- 인스턴스 티켓목록 tickets
- 인스턴스 판매 금액 amount
- 티켓 판매하는 getTicket메서드는 tickets의 맨 첫번째 Ticket을 반환
- 판매 금액 더하거나 차감하는 메서드 plusAmount, minusAmount

### 초대장 교환 및 판매 판매원
- 클래스 판매원 TicketSeller
- 매표소 ticketOffice 근무

### 소극장
- 클래스 소극장 Theater
- 메소드 enter


## 02. 무엇이 문제인가
- 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재
  - 하나의 클래스나 메서드에 너무 많은 세부사항을 다루고 있다.
  - 변경에 취약하다. 최소한의 의존성만을 유지하는 것이 필요하다.


## 03. 설계 개선하기(캡슐화)
### 무엇을
- 매표소 Theater으로부터 관람객 Audience, 판매원 TicketSeller을 자율적인 존재 만들기
  - 관람객 Audience이 가방 Bag, 판매원 TicketSeller이 매표소 ticketOffice를 직접 처리하도록 한다.

### 어떻게
- Theater의 enter 메서드에서 ticketOffice에 접근하는 코드를 TicketSeller로 숨기기
  - TicketSeller에 selTo 메서드를 추가하고 Theater에 있던 로직을 옮긴다.
- Theater의 enter 메서드에서 Bag에 접근하는 코드를 Audience로 숨기기
  - Audience에 buy 메서드를 추가하고 Theater에 있던 로직을 옮긴다.
- TicketSeller가 Audience의 인터페이스에만 의존하도록 수정

### 캡슐화
- 객체는 자신의 데이터를 스스로 처리하는 자율적인 존재
  - 메시지를 통해서만 협력하는 자율적인 객체들의 공동체를 만들어야 한다.
 
### 더 개선하기
- Bag에 hold메서드 추가
  - Audience를 Bag의 인터페이스에 의존
- ticketOffice에 sellTicketTo 메서드 추가
  - TicketSeller가 ticketOffice의 인터페이스에 의존
 
### 트레이드오프
- 어떤 기능을 설계하는 방법은 한 가지 이상일 수 있다.
  - 결합도와 자율성의 모두 만족 시킬 수 있는 방법이 없을때 선택을 하는 것이 트레이드오프 상황이 된다.
  
## 04. 객체지향 설계
- 좋은 객체지향 설계는 협력하는 객체 사이의 의존성을 적절하게 관리하는 것
  


