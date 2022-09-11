## 복습

- 객체지향은 객체를 중심에 놓는 프로그래밍 패러다임
- 좋은 객체지향 설계는 자율적으로 협력하는 객체들의 공동체를 만드는것
- 여기서 객체란 상태와 행동을 함께 캡슐화하는 실행 단위

## 역할, 책임, 협력

- 협력 - 애플리케이션의 기능을 구현하기 위해 수행하는 상호작용
- 책임 - 객체가 협력에 참여하기 위해 수행하는 로직
- 역할 - 객체들이 협력안에서 수행하는 책임들이 모여 구성

### 협력

- **메시지 전송**은 객체사이의 협력을 위해 사용할 수 있는 유일한 수단이다.
- 메시지를 수신한 객체는 메서드를 실행해 요청에 응답한다
    - 객체는 메시지를 처리할 방법을 스스로 선택한다
- 객체의 **행동은 협력이** 결정하고, 객체의 **상태는 행동이** 결정한다
    - 객체는 자신의 상태를 스스로 결정하고 관리하는 자율적인 존재
    - 객체가 수행하는 행동에 필요한 **상태**도 함께 가지고 있어야 한다

### 책임

- 책임은 객체지향 설계의 핵심이다.
- 적절한 협력이 적절한 책임을 제공하고 적절한 책임을 적절한 객체에게 할당해야 단순하고 유연한 설계를 창조할 수 있다.
- 객체의 상태가 아니라 행동에 초점을 두고 설계해야 한다. (행동은 객체의 책임)

**책임 주도 설계**

- 시스템의 책임을 파악한다
- 시스템의 책임을 더 작은 책임으로 분할한다
- 분할된 책임을 수행할 수 있는 적절한 객체를 찾아 책임을 할당한다
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체를 찾는다.
- 해당 객체에 책임을 할당하여 두 객체가 협력하게 한다

### 역할

- 객체가 특정한 협력 안에서 수행하는 책임의 집합
- 역할은 다른 것으로 교체할 수 있는 책임의 집합이다.
    - 역할을 구현하는 가장 일반적인 방법은 추상 클래스와 인터페이스의 사용이다
    - 추상클래스와 인터페이스는 구체 클래스들이 따라야 하는 책임의 집합을 서술한 것
- **역할과 객체를 명확하게 구분하는 것은 그렇게 중요하지 않다. →** 중요한것은 **책임**
    - 객체로 시작해 책임과 협력을 정제해 가며 필요한 순간에 객체로 부터 역할을 분리해 내는것이 가장 좋은 방법

## 읽으면서

> 역할은 다른 것으로 교체할 수 있는 책임의 집합이다.
> 

역할을 책임의 집합이라고 하길래 순간 하나의 객체가 여러 책임을 진다면 SRP를 위반한것이 아닌가 하는 생각이 들었다. 이후 객체가 아니라 추상클래스와 인터페이스를 역할의 구현이라는 설명을 듣고 이해가 되었다.

> 객체로 시작해 책임과 협력을 정제해 가며 필요한 순간에 객체로 부터 역할을 분리해 내는것이 가장 좋은 방법이다
> 

개발하면서 모든 서비스레이어 클래스들에 대해 인터페이스를 정의한 적이 있는데 불필요하게 보일러플레이트 코드를 양산한다는 생각이 들었다. 객체로 시작해 책임과 협력을 정제해 가며 필요한 순간에 객체로 부터 역할을 분리해 내는것이 가장 좋은 방법이라면 굳이 모든 객체에 대해 인터페이스를 정의할 필요는 없을 것 같다.

## 알고리즘 문제

오브젝트를 읽으며 객체지향적으로 카카오 코딩테스트 문제를 풀어보았다.

[코딩테스트 연습 - 주차 요금 계산](https://school.programmers.co.kr/learn/courses/30/lessons/92341)

문제를 보고 3가지 책임(객체)를 추려보았다.

![image](https://user-images.githubusercontent.com/28651727/189522960-44150892-5230-4f36-ba70-e23bbb6317dd.png)


### ParkingLot (주차장)

```java
class ParkingLot {

    private final Map<Integer, Car> carMap;
    private final Fee fee;

    public ParkingLot(Fee fee) {
        this.carMap = new HashMap<>();
        this.fee = fee;
    }

    public void processComeAndOut(Record record) {
        switch (record.type()) {
            case IN -> parkIn(record.carNum(), record.time());
            case OUT -> parkOut(record.carNum(), record.time());
        }
    }

    public CalculateResult calculate() {
        List<Integer> carNums = getCarNums();

        List<Integer> results = carNums.stream()
                .mapToInt(carNum -> carNum)
                .map(carNum -> carMap.get(carNum).getCumulativeTime())
                .map(fee::getCost)
                .boxed()
                .toList();

        return CalculateResult.from(results);
    }

    public void close() {
        Time exitTime = Time.from("23:59");
        carMap.values()
                .stream()
                .filter(car -> car.getStatus().equals(Car.Status.IN))
                .forEach(car -> car.parkOut(exitTime));
    }

    private List<Integer> getCarNums() {
        List<Integer> carNums = new ArrayList<>(carMap.keySet());
        carNums.sort(Comparator.naturalOrder());
        return carNums;
    }

    private void parkIn(int carNum, Time time) {
        if (!carMap.containsKey(carNum)) {
            carMap.put(carNum, new Car(time));
        }

        carMap.get(carNum).parkIn(time);
    }

    private void parkOut(int carNum, Time time) {
        Car car = carMap.get(carNum);
        car.parkOut(time);
    }
}

```

차량 출입관리의 책임도 따로 나눌 수 있을것 같다.

### Car

```java
class Car {
    private int cumulativeTime;
    private Time lastUpdateTime;
    private Status status;

    public Car(Time time) {
        this.cumulativeTime = 0;
        this.lastUpdateTime = time;
        this.status = Status.IN;
    }

    public void parkIn(Time time) {
        this.lastUpdateTime = time;
        this.status = Status.IN;
    }

    public void parkOut(Time time) {
        this.cumulativeTime += Time.getDuration(this.lastUpdateTime, time);
        this.lastUpdateTime = time;
        this.status = Status.OUT;
    }

    public int getCumulativeTime() {
        return cumulativeTime;
    }

    public Status getStatus() {
        return status;
    }

    enum Status {
        IN, OUT
    }
}
```

### Fee

```java
class Fee {
    private final int defaultTime;
    private final int defaultCost;
    private final int unitTime;
    private final int unitCost;

    public Fee(int[] fees) {
        this.defaultTime = fees[0];
        this.defaultCost = fees[1];
        this.unitTime = fees[2];
        this.unitCost = fees[3];
    }

    int getCost(int duration) {
        if (duration <= defaultTime) {
            return defaultCost;
        }

        return defaultCost + ((duration - defaultTime + unitTime - 1) / unitTime) * unitCost;
    }

}
```

### VO - Record, Time, CalculateResult

```java
record Record(
        int carNum,
        Type type,
        Time time
) {
    public static Record from(String s) {
        String[] ss = s.split(" ");
        int carNum = Integer.parseInt(ss[1]);
        Type type = Type.valueOf(ss[2]);
        Time time = Time.from(ss[0]);

        return new Record(carNum, type, time);
    }

    enum Type {
        IN, OUT
    }

}

record Time(
        int hour,
        int minute
) {

    public static Time from(String s) {
        String[] ss = s.split(":");
        int hour = Integer.parseInt(ss[0]);
        int minute = Integer.parseInt(ss[1]);
        return new Time(hour, minute);
    }

    public static int getDuration(Time start, Time end) {
        return (end.hour - start.hour) * 60 + end.minute - start.minute;
    }

}

record CalculateResult (
        int[] arr
) {
    static CalculateResult from(List<Integer> list) {
        int[] result = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            result[i] = list.get(i);
        }
        return new CalculateResult(result);
    }

}
```