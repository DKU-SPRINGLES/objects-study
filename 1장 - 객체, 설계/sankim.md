# 오브젝트 1장 - 객체, 설계
## 로버트 L. 글래스 - 이론 vs 실무
건축처럼 역사가 오래된 여느 다른 공학 분야에 비해 상대적으로 짧은 소프트웨어 분야의 역사를 감안 했을 때, 소프트웨어 분야는 아직 걸음마 단계다. 따라서 이론보다 실무가 더 앞서 있으며, 실무가 더 중요하다.

## 로버트 마틴 - 소프트웨어 모듈의 세 가지 목적
1. 실행 중에 제대로 동작해야 한다.
2. 변경을 위해 존재해야 한다.
3. 코드를 읽는사람과 의사소통해야 한다.


## 예상을 빗나가는 코드

```java
    public class Theater {
    	private TicketSeller ticketSeller;
    
    	public Theater(TicketSeller ticketSeller) {
    		this.ticketSeller = ticketSeller;
    	}
    
    	public void enter(Audience audience) {
    		if(audience.getBag().hasInvitation()) {
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

이해가능한 코드란 그 동작이 우리의 예상에서 크게 벗어나지 않는 코드다. 현실에서는 관람객이 직접 자신의 가방에서 초대장을 꺼내 판매원에게 건네지만, 앞선 코드에서는 그렇지않다. 이는 우리의 상식과 너무나 다르게 동작하기에, 코드를 읽는 사람과 제대로 의사소통 하지 못한다.

또한 `Theater`의 enter메서드를 이해하기 위해서는 `Audience`와 Bag의 내부 구현을 이해하고 있어야 한다. enter 메서드는 다른 도메인의 너무 많은 세부사항을 다루기 때문에 코드를 읽고 이해해야 하는 사람 모두에게 큰 부담을 준다.

## 변경에 취약한 코드
`Theater`가 `Audience`와 Bag의 세부사항에 강하게 의존하고 있기에 해당 객체들의 세부사항이 변경되면, Theater도 변경이 되어야 한다.

객체 사이의 의존성을 완전히 없애는 것이 정답은 아니다. 객체 지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는것이다.

따라서 우리의 목표는 최소한의 의존성만 유지하고, 불필요한 의존성을 제거하는 것이다.

## 자율적인 존재로 만들자
`Theater`가 `Audience`와 `TicketSeller`에 관해 너무 세세한 부분까지 알 필요가 없도록, 관람객이 스스로 가방안의 현금과 초대장을 처리하고, 판매원이 스스로 매표소의 티켓과 판매 요금을 다루게 하면 된다.

즉, 관람객과 판매원을 *자율적인 존재*로 만들면 되는 것.

티켓을 관람객의 가방에서 꺼내서 확인하고 판매하는 역할은 *소극장의 역할이 아니다*. 가방에서 티켓을 꺼내는것은 *관람객의 역할*이며, 티켓을 판매하는 역할은 *판매원의 역할*이다.

각자의 역할에만 충실하도록 코드를 변경하자.

### 티켓 판매

소극장 -> 판매원

```java
    public class Theater {
    	private TicketSeller ticketSeller;
    
    	public Theater(TicketSeller ticketSeller) {
    		this.ticketSeller = ticketSeller;
    	}
    
    	public void enter(Audience audience) {
    		ticketSeller.toSell(audience);
    	}
    }
```

```java
    public class TicketSeller {
    	private TicketOffice ticketOffice;
    
    	public TicketSeller(TicketOffice ticketOffice) {
    		this.ticketOffice = ticketOffice;
    	}
    
    	public void toSell(Audience audience) {
    		if(audience.getBag().hasInvitation()) {
    			Ticket ticket = ticketOffice.getTicket();
    			audience.getBag().setTicket(ticket);
    		} else {
    			Ticket ticket = ticketOffice.getTicket();
    			audience.getBag().minusAmount(ticket.getFee());
    			ticketOffice.plusAmount(ticket.getFee());
    			audience.getBag().setTicket(ticket);
    		}
    	}
    }

```

`TicketSeller` 에서 getTicketOffice메서드가 제거되어 외부에서는 `TicketOffice`에 접근할 수 없게되었다. 따라서 `TicketSeller`는 `TicketOffice`에 대한 작업을 스스로 수행해야한다.

이렇게 객체 내부의 세부사항을 감추는것을 캡슐화 라고 하며, 캡슐화를 통해 객체와 객체 사이의 결합도를 낮추어 변경이 쉬운 객체를 만들 수 있다.

### 티켓 확인

소극장 -> 관람객
```java
    public class TicketSeller {
    	private TicketOffice ticketOffice;
    
    	public TicketSeller(TicketOffice ticketOffice) {
    		this.ticketOffice = ticketOffice;
    	}
    
    	public void toSell(Audience audience) {
    		ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    	}
    }

```

```java
    public class Audience {
    	private Bag bag;
    
    	public Audience(Bag bag) {
    		this.bag = bag;
    	}
    
    	public Long buy(Ticket ticket) {
    		if(bag.hasInvitation()) {
    			bag.setTicket(ticket);
    			return 0L;
    		} else {
    			bag.setTicket(ticket);
    			bag.minusAmount(ticket.getFee());
    			return ticket.getFee();
    		}
    	}
    }
```

`TicketSeller`는 `Audience`의 buy 메서드에만 의존하게 된다.  즉 관객이 스스로 가방을 확인하고 티켓 구매를 진행한다. 외부에는 `Bag`을 노출하지 않게 되므로 Bag의 존재를 내부로 캡슐화 할 수 있게 된다.

## 개선점

수정된 `Audience`와 `TicketSeller`는 자신이 가지고 있는 소지품을 스스로 관리한다. 이것은 우리의 예상과 정확하게 이라하며, 코드를 읽는 사람과의 의사소통이라는 관점에서 개선된 것이다.

또한 `Audience`와 `TicketSeller`의 내부 구현을 변경하더라도 `Theater`를 변경 할 필요가 없으므로 변경 용이성 측면에서도 개선이 된것이다.

*객체지향의 핵심*은 캡슐화를 이용해 의존성을 적절히 관리함으로써 객체 사이의 결합도를 낮추는 것이다. 객체지향 코드는 각 객체의 문제를 스스로 처리한다는 점에서 이해하기 쉽고, 객체 내부의 변경이 외부에 영향을 주지 않으므로 변경하기가 수월하다.

## 개선 방향
설계를 어렵게 만드는 것은 *의존성*이다. 다른 객체가 몰라도 되는 불필요한 세부사항을 객체 내부로 *캡슐화*하여 자율적인 객체들이 낮은 결합도와 높은 응집도를 가지고 협력하도록 최소한의 의존성을 남기는것이 휼륭한 객체지향 설계다.

## 더 개선해 보기
`TicketSeller`와  `Audience`는 자율적인 객체가 되었다. 하지만 `TicketOffice`와 `bag`은 여전히 수동적인 존재이다. 이 둘도 자율적인 객체로 개선할 수 있다.

```java
   public class Bag {
   	private Long amount;
   	private Invitation invitation;
   	private Ticket ticket;
   
   	public Bag(Long amount) {
   		this(null,amount);
   	}
   
   	public Bag(Invitation invitation, long amount) {
   		this.amount = amount;
   		this.invitation = invitation;
   	}
   
   	public Long hold(Ticket ticket) {
   		if(hasInvitation()) {
   			setTicket(ticket);
   			return 0L;
   		} else {
   			setTicket(ticket);
   			minusAmount(ticket.getFee());
   			return ticket.getFee();
   		}
   	}
   
   	private boolean hasInvitation() {
   		return invitation != null;
   	}
   
   	public boolean hasTicket() {
   		return ticket != null;
   	}
   
   	private void setTicket(Ticket ticket) {
   		this.ticket = ticket;
   	}
   
   	private void minusAmount(Long amount) {
   		this.amount -= amount;
   	}
   
   	public void plusAmount(Long amount) {
   		this.amount += amount;
   	}
   }

```

```java
    public class Audience {
      public Long buy(Ticket ticket) {
        return bag.hold(ticket);
      }
    }
```

캡슐화를 통해 `Audience`는 `bag`의 인터페이스에만 의존하게 된다.

```java
    public class TicketOffice {
    	private Long amount;
    	private List<Ticket> tickets = new ArrayList<>();
    
    	public TicketOffice(Long amount, Ticket ... tickets) {
    		this.amount = amount;
    		this.tickets.addAll(Arrays.asList(tickets));
    	}
    
    	public void sellTicketTo(Audience audience) {
    		plusAmount(audience.buy(getTicket()));
    	}
    
    	private Ticket getTicket() {
    		return tickets.remove(0);
    	}
    
    	public void minusAmount(Long amount) {
    		this.amount -= amount;
    	}
    
    	private void plusAmount(Long amount) {
    		this.amount += amount;
    	}
    
    }

```

```java
    public class TicketSeller {
      public void sellTo(Audience audience) {
        ticketOffice.sellTicketTo(audience);
      }
    }
```

캡슐화를 통해 `TicketSeller `는 `TicketOffice`의 인터페이스에만 의존하게 된다. 하지만 `TicketOffice`는 판매를 위해 `Audience`에 의존하게 된다. 새로운 의존성이 추가되었으므로, 전체 설계의 관점에서는 결합도가 상승한것이다.

결합도를 낮추는것이 우선일까 각 객체의 자율성을 만족시키는것이 우선일까?
설계는 트레이드오프의 산물이다. 모든 사람을 만족시킬 수는 없다.

## 의인화
`bag`과 `TicketOffice`, `Theater`는 현실세계에서는 자율적인 존재가 아니다. 하지만 객체지향 세계에서는 모든 객체들이 자율적으로 행동한다.
이처럼 모든 객체를 능동적이고 자율적인 존재로 객체를 설계하는 원칙을 *의인화* 라고 한다.

## 좋은 설계
요구사항이 항상 변경되기에 변경을 수용할 수 있는 설계가 중요하다. 또한 코드를 변경할 때 버그가 추가될 가능성이 높기 때문에 최소한의 변경으로 요구사항을 만족시킬 수 있어야 한다.

변경할 수 있는 코드는 이해하기 쉬운 코드다. 이해하기 쉬운 코드만이 코드를 선뜻 수정하게 만든다.

## 개인적으로
각 객체의 자율성을 만족시키는 것이 우선일까? 결합하는 낮추는 것이 우선일까? 트레이드오프는 개발 공부를 하면서 매번 듣고 느끼는 단어입니다.

협업하면서 정답이 없는 문제에 대해 논쟁하다 보면 정말 정답은 없지만, 상황에 따라 조금 더 타당한 논리가 지지받습니다. 때문에 코드 작성 만큼 다양한 관점에서의 논리와 해석을 많이 찾아보면서 자신만의 논리를 찾아가는 과정도 중요하다 생각합니다.

오브젝트 책을 통해 논리가 분명한 설계를 할 수 있게 되길 바랍니다.
