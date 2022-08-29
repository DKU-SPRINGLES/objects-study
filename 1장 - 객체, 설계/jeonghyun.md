### 프로그래밍 패러다임

- 프로그래밍 패러다임은 개발자 공동체가 동일한 프로그래밍 스타일과 모델을 공유할 수 있게 함으로써 불필요한 부분에 대한 **의견 충돌을 방지**한다.
- 즉, 같은 패러다임 내에서는 개발자들이 **동일한 규칙과 표준**에 따라 프로그램을 작성할 수 있다.
- **은총알은 없다.**
  - 객체지향 패러다임이 항상 정답이 아니다.
  - 언제라도 다른 패러다임을 적용할 수 있는 시야가 필요하다.

# 1장 객체, 설계

- 소프트웨어 분야는 이론보다 실무가 앞서 있으며 실무가 더 중요하다.
- 특히나 '소프트웨어 설계'와 '소프트웨어 유지보수'는 실무가 이론보다 앞서 있는 대표적인 분야로 위와 관련된 효과적인 이론이 발표된 적이 거의 없다.
- 결론적으로 소프트웨어 설계와 유지보수에 중점을 두려면 **이론이 아닌 실무에 초점을 맞추는 것이 효과적이다.**

## 티켓판매 애플리케이션

### 요구사항 분석

- 소극장에 입장하기 위해서는 티켓이 필요하다.
- 이벤트에 당첨된 관람객은 초대장을 갖고 있다.
- 매표소에서는 현금으로 티켓을 구매할 수 있다.
- 매표소에서는 초대장을 티켓으로 교환할 수 있다.

### V1 - 변경에 취약한 코드

```java
public class Theater {
	private TicketSeller ticketSeller;

	public Theater(TicketSeller ticketSeller) {
		this.ticketSeller = ticketSeller;
	}
	
	public void enter(Audience audience) {
		if (audience.getBag().hasInvitation()) {
			Ticket ticket = ticketSeller.getTicketOffice().getTicket();
			audience.getBag().setTicket(ticket);
		} else {
			Ticket ticket = ticketSeller.getTicketOffice().getTicket();
			audience.getBag().minusAmount(ticket.getFee());
			ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
			audience.getBag().setTicket(ticket);
		}
	}
}
```

- 현재 `Theater` 클래스는 `Audience`, `TicketSeller`, `TicketOffice`, `Ticket`에 의존성을 갖고있다.
- 이렇게 객체 사이에 의존성이 과한 경우를 가리켜 **결합도**가 높다고 말한다.
- 결합도가 높으면 변경에 취약하다.

### V2 - 자율성을 높인 코드

```java
public class Theater {
	private TicketSeller ticketSeller;
}

public Theater(TicketSeller ticketSeller) {
	this.ticketSeller = ticketSeller;
}

public void enter(Audience audience) {
		ticketSeller.sellTo(audience)
	}
}
```
- `TicketSeller`에 `sellTo()` 메서드를 추가하여 `TicketOffice`에 접근하는 모든 코드를 `TicketSeller` 내부로 숨긴다.
- `Teater`는 단지 `ticketSeller`의 `sellTo()` 메시지만 이해하고 응답한다.

```java
public class TicketSeller {
  private TicketOffice ticketOffice;
  
  public TicketSeller(TicketOffice ticketOffice) {
    this.ticketOffice = ticketOffice;
  }
}	

public void sellTo(Audience audience) {
  if (audience.getBag().hasInvitation()) {
		Ticket ticket = ticketOffice.getTicket();
		audience.getBag().setTicket(ticket);
	} else {
		Ticket ticket = ticketOffice.getTicket();
		audience.getBag().minusAmount(ticket.getFee());
		ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
		audience.getBag().setTicket(ticket);
}
```

- `TicketSeller` 에서는 더 이상 `getTicketOffice()` 메서드가 필요하지 않다.
- `TicketSeller`는 `ticketOffice`에서 티켓을 꺼내거나 판매 요금을 적립하는 일을 스스로 수행할 수 있다.
- 이처럼 개념적이나 물리적으로 객체 내부의 세부적인 사항을 감추는 것을 **캡슐화**라고 부른다.

## 설계 개선하기

### 캡슐화와 응집도

- 객체의 **자율성**을 높이는 방향으로 설계를 한다면, 이해하기 쉽고 유연한 객체를 얻을 수 있다.
- 객체는 자신의 데이터를 스스로 처리하고 객체 간에 상호작용은 오직 메시지를 통해서만 이루어져야 한다.
- 위와 같은 방식으로 객체 내부의 상태를 캡슐화하면 결합도를 낮추고 응집도를 높일 수 있다.

### 절차지향과 객체지향

- 절차적 프로그래밍은 프로세스가 필요한 모든 데이터에 의존해야 한다.
  - 변경에 취약하다.
- 객체지향 프로그래밍은 데이터와 프로세스가 동일한 모듈 내부에 위치한다.
  - 의존성을 적절히 통제할 수 있다.

### 책임의 이동

1. **의존성**은 설계를 어렵게 만든다.
2. **의존성**을 제거하려면 객체 사이의 **결합도**를 낮추어야 한다.
3. **결합도**를 낮추기 위해서는 협력하는 객체들 각각의 세부사항을 내부로 감춰 **캡슐화**한다.
4. 결과적으로 **캡슐화**를 통해 객체의 **자율성**을 높이고 **응집도** 높은 객체들의 공동체가 가능하다.

> 결론: 불필요한 세부사항을 캡슐화하여 최소한의 의존성만 남겨라.

### 더 개선할 수 있다

이번 소제목에서는 `TicketOffice`의 자율성을 높여준다. 

- 기존의 코드에서는 `TicketSeller`가 `TicketOffice` 에 있는 `Ticket`을 맘대로 꺼내갔다. 이를 해결하기 위해 `TicketOffice`에 `sellTicketTo()` 메서드를 추가하여 `TicketOffice`가 주체적으로 `Audience`에게 `Ticket`을 판매할 수 있게 개선하였다. 
- 한 가지 재미있는 점은 이 과정에서 `TicketOffice`가  `Audience`에 대한 의존성이 추가되었다는 사실이다.즉, **객체간의 자율성을 만족시키다보니 결합도가 높아지는 현상이 발생한 것이다.** 이러한 트레이드 오프 시점에서 우리는 적절한 설계를 끊임없이 고민해보아야 한다.

### 그래 거짓말이다!

이 부분은 '객체지향의 사실과 오해'의 앞 부분에서 자세하게 다루었던 부분이라 가볍게 읽고 넘어갔다.

## 객체지향 설계

> 설계란 코드를 배치하는 것이다.

- 좋은 설계란 변경에 유연하게 대응할 수 있는 설계이다.
- 변경 가능한 코드란 이해하기 쉬운 코드다.

### 객체지향 패러다임

- 객체지향 패러다임은 우리가 세상을 바라보는 방식대로 코드를 작성할 수 있게 돕는다.
  - 세상에 존재하는 모든 자율적인 존재처럼 객체 역시 자신의 데이터를 스스로 책임지는 자율적인 존재다.