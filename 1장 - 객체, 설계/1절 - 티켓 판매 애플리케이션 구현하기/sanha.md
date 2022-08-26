`패러다임` - 한 시대의 사회 전체가 공유하는 이론 혹은 방법.
절차형 → 객체지향으로 패러다임 전환을 맞았다.

프로그래밍 패러다임은 과거의 패러다임을 폐기시키는 혁명적 패러다임이 아니라 과거의 패러다임을 개선하는 `발전적 패러다임`이다.

객체지향이 적합하지 않은 상황에서는 언제라도 다른 패러다임을 적용할 수 있는 시야와 지식을 길러야한다.

## 티켓 판매 애플리케이션

작은 소극장을 운영하고 있으며 무료로 관람할 수 있는 초대장을 발송하는 이벤트를 열었다.

- 이벤트 당첨 관람객과 일반 관람객을 구분해야한다.

- **일반 관람객**
	- 티켓을 매표소에서 구매하여 입장
- **당첨 관람객**
	- 매표소에서 초대장과 티켓을 교환하여 입장.

``` java
// 초대장 객체
public class Invitation{
    private LocalDateTime when;
}

// 티켓 객체
public class Ticket{
    private Long fee;
    
    public Long getFee(){
        return fee;
    }
}
```

관람객은 초대장, 현금, 티켓 3가지 소지품을 가방에 갖고 있다.

``` java
public class Bag{
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;
    
    public boolean hasInvitation() {
        return invitaion != null;
    }
    
    public boolean hasTicket(){
        return ticket != null;
    }

    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    public void minusAmount(Long amount) {
        this.amoun -= amount;
    }

    public void plusAmount(Long amount) {
        this.account += account;
    }
}
```

가방에 초대장이 있는 경우와 없는 두가지 경우가 있을 수 있기 때문에 Bag의 인스턴스 생성 시점에 해당 제약을 강제할 수 있도록 생성자를 추가한다.

``` java
public class Bag{
    public Bag(long amount){
        this(null, amount);
    }

    public Bag(Invitation invitation, long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }
}
```

관람객이 가방을 갖는걸 표현하면 다음과 같다.

``` java
// 관람객 객체
public class Audience{
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }
    
    public Bag getBag() {
        return bag;
    }
}
```

매표소에는 관람객에게 판매할 티켓과 티켓 판매 금액을 갖고 있어야 한다.

``` java
// 매표소 객체
public class TicketOffice{
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }
    
    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

- 판매원은 매표소에서 초대장을 티켓으로 교환해 주거나 티켓을 판매하는 역할을 수행.

- 판매원은 자신이 일하는 매표소를 알고 있어야 한다.

``` java
public class TicketSeller{
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

소극장은 관람객을 맞이하는 역할을 수행할 수 있어야 한다.

``` java
public class Theater{
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