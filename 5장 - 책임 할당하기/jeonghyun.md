# 책임 할당하기

### 책임 주도 설계

- 협력에 적합한 책임이란 메시지 수신자가 아니라 **메시지 전송자**에게 적합한 책임을 의미한다.
- 객체가 메시지를 선택하는 것이 아니라 **메시지가 객체를 선택**해야 한다.
- 메시지를 수신하기로 결정된 객체는 메시지를 처리할 '책임'을 할당받게 된다.

## GRASP 패턴

> 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들

- 도메인 개념
  - 도메인 개념을 완벽하게 정리할 필요없다.

### 정보 전문가 패턴

- 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것
- 정보를 저장하고 있을 필요가 없다.
- "정보를 제공할 수 있는 다른 객체를 알고 있거나" or "정보를 계산해서 제공하거나"

### LOW COUPLING / HIGH COHENSION 패턴

낮은 결합도 / 높은 응집도 패턴.

- 책임을 할당할 수 있는 다양한 대안들이 존재한다면 결합도가 낮고, 응집도가 높은 쪽을 선택하라.

### CREATEOR 패턴

창조자 패턴.

A 객체를 생성해야 할 때 아래 조건을 최대한 많이 만족하는 B 객체에게 생성 책임을 할당해라.

- B가 A 객체를 포함하거나 참조
- B가 A 객체를 기록
- B가 A 객체를 긴밀하게 사용
- B가 A 객체를 초기화하는 데 필요한 데이터를 갖고 있음.

### POLYMORPHISM 패턴

다형성 패턴.

- `if ~ else`, `switch ~ case` 등의 논리 조건을 사용하는 대신
- 다형성을 이용해 행동을 나누어라.

### PROTECTED VARIATINOS 패턴

변경 보호 패턴.

- 변경이 될 가능성이 있는 지점을 인터페이스로 만들어라.
- 변하는 개념을 캡슐화해라.

## 구현을 통한 검증

### 변경과 유연성

변경에 대비하는 두 가지 방법

1. 코드를 이해하고 수정하기 쉽도록 최대한 단순하게 설계한다.
2. 변경을 수용할 수 있도록 더 유연하게 만든다.

- 위 두 가지 방법은 복잡성과 유연성이라는 트레이드 오프 관계이다.
  따라서 변경이 얼마나 반복적으로 발생하는지에 따라 적합한 방법을 결정한다.

### 코드의 구조와 도메인 구조

- 도메인 모델은 구현과 밀접한 관계를 맺어야 한다.
- 코드의 변화에 발맞춰 함께 변환해야 한다.

## 책임주도 설계의 대안

- 적절한 책임을 찾기 어렵다면 일단 실행되는 코드를 얻고 난 후에 리팩터링하자.
- 몬스터 메서드를 쪼개라.

## 느낀점

처음부터 완벽한 설계를 할 수 없다는 것을 인정하고 우선 코드를 완성하는 것이 중요하다는 것을 배웠다.

특히나 취업을 준비하고 있는 이 시점에서 한 번에 멋진 설계를 하는 것보다 설계가 어떻게 발전해왔고, 중간에 어떠한 고민들을 했는지 잘 정리해 녹여낸다면 횔씬 더 깊이있어 보이는 구직자가 될 수 있겠다는 생각을 했다.

또한 항상 유연한 설계가 좋을 것이라 생각했는데 코드의 복잡성과 유연성은 트레이드 오프 관계이고, 무조건 유연한 설계만을 고집할 필요가 없다는 것을 배웠다. 

