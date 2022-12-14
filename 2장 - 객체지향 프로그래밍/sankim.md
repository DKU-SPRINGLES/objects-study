이번장은 누군가 객체지향에 대한 추상적인 개념들을 물었을때, 책에서 설명한 부분들을 인용해 설명하면 좋겠다는 생각이 많이 들었다.

## 클래스의 내부와 외부를 구분해야 하는 이유

### 경계의 명확성이 객체의 자율성을 보장한다

- 객체 내부에 대한 접근을 통제하는것은 외부의 간섭을 최소화하고, 객체가 자율적으로 행동할 수 있도록 한다.
- 퍼블릭 인터페이스 : 객체 외부에서 접근가능한 부분
- 구현 : 객체 내부에서만 접근가능한 부분

### 프로그래머에게 구현의 자유를 제공한다

- 객체의 외부와 내부를 구분하면 클라이언트 프로그래머가 알아야 할 지식의 양이 줄어들고, 클래스 작성자는 자유롭게 구현을 변경할 수 있는 폭이 넓어진다.
- 변경에 대한 파급효과를 제어하기 위해 접근제어를 활용하라

## 협력

- 메시지 전송 : 객체가 다른 객체와 상호작용하기 위한 유일한 방법
- 메시지 수신 : 다른 객체의 요청이 도착했을때
- 메서드 : 수신된 메시지를 처리하기 위한 방법

## 코드의 의존성과 실행 시점의 의존성은 다를 수 있다

코드상으로는 DiscountPolicy에 의존하고 있지만, 실행시점에 Moview 인스턴스는 DiscountPolicy의 구현체인 AmountDiscountPolicy, PercentDiscountPolicy의 인스턴스에 의존하게 된다. 이렇게 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 이해하기 어려워 지지만, 코드는 더 유연해지고 확장에 유리해진다.

**다형성**은 이러한 사실을 기반으로 동일한 객체의 메시지에 다르게 응답할 수 있는 능력을 의미한다

## 상속은 캡슐화를 위반한다

상속을 이용하기 위해서는 부모클래스의 구조를 잘 알고 있어야 하며, 결과적으로 부모 클래스의 구현을 자식클래스에 노출하기 때문에 캡슐화가 약화된다. 또한 컴파일 시점에 부모 클래스와 자식 클래스의 관계가 결정되기 때문에 유연한 설계가 불가능하다.

반면 인스턴스 변수로 관계를 설정하면 객체가 서로 내부 구현에 대해서 알 필요가 없고, 설계가 유연해진다.

이렇게 인터페이스에 정의된 메시지를 통해 코드를 재사용하는 방법을 합성이라 한다.
