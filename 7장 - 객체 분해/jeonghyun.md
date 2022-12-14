# 객체 분해

- **추상화** : 문제 해결에 필요한 핵심만 남기는 작업
- **분해** : 큰 문제를 해결 가능한 작은 문제로 나누는 작업

## 프로시저 추상화와 데이터 추상화

### 프로시저 추상화

- 소프트웨어가 무엇을 **해야 하는지**를 추상화한다.
- 기능 분해 / 알고리즘 분해

### 데이터 추상화

- 소프트웨어가 무엇을 **알아야 하는지**를 추상화한다.
- 데이터를 중심으로
  - **타입**을 추상화하면 `추상 데이터 타입`
  - **프로시저**를 추상화하면 `객체지향`

## 프로시저 추상화와 기능 분해

### 하향식 접근법

시스템을 최상위의 가장 추상적인 메인 함수로 정의하고, 메인 함수를 구현 가능한 수준까지 세부적인 단계로 분해하는 방법이이다.

#### 문제점

- 대부분의 시스템에서는 하나의 메인 기능이란 개념이 존재하지 않는다.
- 함수는 상위 함수가 강요하는 문맥에 강하게 결합된다.
- 처음부터 함수의 실행순서를 결정하기 때문에 함수의 재사용성이 떨어진다.
- **어떤 데이터를 어떤 함수가 사용하고 있는지 추적하기 어렵다.**
  - 데이터의 형식이 변경될 경우 모든 함수를 다 열어봐야 한다.
  - 의존성과 결합도에 의한 문제

## 모듈

### 정보 은닉과 모듈

- 변경에 대한 영향을 최소화하기 위해 영향을 받는 부분과 받지 않는 부분을 명확하게 **분리해야 한다.**
- 외부에 감춰야 하는 **비밀**을 선택하고 **방어막**을 쳐서 보호해라.
  - 여기서 **방어막**은 퍼블릭 인터페이스를 의미한다.
  - 가장 일반적인 **비밀**은 데이터다.


### 모듈의 장점과 한계

#### 장점

- 모듈 내부의 변수가 변경되더라도 모듈 내부에만 영향을 미친다.
  - 영향을 받는 함수를 찾기 위해 해당 데이터를 정의한 모듈만 검색하면 된다.
- 비즈니스 로직과 사용자 인터페이스에 대한 관심사를 분리한다.
- 전역 변수와 전역 함수를 제거함으로써 네임스페이스 오염을 방지한다.

#### 한계

- 인스턴스의 개념을 제공하지 않는다.
- 이를 만족시키기 위해 "추상 데이터 타입"이라는 개념이 등장하였다.

## 데이터 추상화와 추상 데이터 타입

### 추상 데이터 타입

#### 기본 의도

- 프로그래밍 언어가 제공하는 타입처럼 동작하는 사용자 정의 타입
- 모든 개념적인 타입에 대한 구현을 포괄하도록 함으로써 하나의 물리적인 타입 안에 전체 타입을 감춘다.

#### 한계

여전히 데이터와 기능을 분리하는 절차적인 설계의 틀에 갖혀 있다.

## 클래스

### 클래스는 추상 데이터 타입인가?

- 추상 데이터 타입
  - 오퍼레이션을 기준으로 타입을 묶는다.
  - **타입을** 추상화한 것
  - 상속과 다형성을 지원하지 않는다.
- 클래스
  - 타입을 기준으로 오퍼레이션을 묶는다.
  - **절차**를 추상화한 것
  - 상속과 다형성을 지원한다.

### 변경을 기준으로 선택하라

#### 클래스가 추상 데이터 타입의 개념을 따라는지 확인하는 방법

- 클래스 내부에 인스턴스의 타입을 표현하는 변수가 있는지 확인한다.
- 인스턴스 변수에 저장된 값을 기반으로 메서드 내에서 타입을 명시적으로 구분하는 방식은 객체지향을 위반하는 것으로 간주된다.
- 객체지향에서는 타입 변수를 이용한 조건문을 다형성으로 대체한다.

#### 개방-폐쇄 원칙 (OCP)

기존 코드에 아무런 영향도 미치지 않고 새로운 객체 유형과 행위를 추가할 수 있는 특성

#### 항상 절차를 추상화하는 객체지향 설계 방식을 따라야 하는가?

- 설계의 유용성은 변경의 **방향성**과 **발생 빈도**에 따라 결정된다.
- 변경의 압력이 "**타입 추가**"에 관한 것이라면,
  - 객체지향이 유리하다. (책임-주도 설계)
  - 새로운 타입을 추가하기 위해 새로운 클래스를 상속 계층에 추가하기만 하면 된다.
- 변경의 압력이 "**오퍼레이션 추가**"에 관한 것이라면,
  - 추상 데이터 타입이 유리하다. (데이터-주도 설계)
  - 객체지향을 기반으로 설계한 클래스에는 인터페이스와 구현체를 모두 수정해야 한다.
  - 하지만, 추상 데이터 타입에는 전체 타입에 대한 구현 코드가 하나의 구현체 내에 있기 때문에 오퍼레이션을 추가하는 작업이 상대적으로 간단하다.
- 이처럼 객체지향적인 접근법이 모든 경우에 올바른 해결 방법은 아니다.
- **변경의 축을 찾아라.**

## 느낀점

 새로운 개념이나 원칙들을 배우면서 가장 우려되는 점은 마치 정답을 알고 있는 것처럼 설계를 한다는 것이다. 

무작정 "어느 방식이 좋다더라", "이러한 패러다임이 트렌드더라" 하는 식의 논리를 갖고 설계를 해서는 안된다. 

설계는 트레이드 오프의 산물이라는 말을 항상 명심하고, 다양한 방식으로 사고하는 습관을 들여야겠다.