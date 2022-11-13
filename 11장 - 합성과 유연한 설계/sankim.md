# 오브젝트 - 합성과 유연한 설계

## 상속과 합성의 차이

상속 관계는 is-a관계라고 부르고 합성 관계는 has-a관계라고 부른다. 둘은 코드 재사용이라는 공통점이 있지만 구현 방식과 다루는 방식에 있어서 큰 차이를 보인다.

상속은 자식클래스가 부모 클래스의 내부 구현에 대해 상세하게 알아야 하기 때문에 부모 클래스와 강한 결합도를 가진다. 둘의 관계는 컴파일 타임에 결정된다.

반면 합성은 객체의 구현이 아닌 내부에 포함되는 인터페이스에 의존하며 객체 내부 구현이 변경되더라고 영향을 최소화 할 수 있다. 또한 런타임 시점에 동적으로 변경할 수 있어 변경하기 쉽고 유연한 설계가 가능하다.

따라서 코드 재사용을 위해서는 객체 합성이 클래스 합성보다 더 좋은 방법이다.

## 상속에서 합성으로 변경하기

**상속의 단점**
1. 불필요한 인터페이스를 상속하게 된다.
2. 부모클래스의 메서드 호출방법에 의존하게 된다.
3. 부모클래스의 변경이 자식클래스에게 영향을 미치게 된다.

상속관계는 컴파일 타임에 결정되기 때문에 부가기능을 추가하고자 한다면, 모든 가능한 조합의 클래스를 생성해야한다는 단점이 있다. 합성은 컴파일 타임의 정적인 관계를 런타임의 동적인 관계로 변경하여 이러한 문제를 해결할 수 있다.

상속이 아니라 합성을 통해 기존 객체의 코드를 재사용하면서 부가기능을 추가하는 대표적인 사례가 데코레이터 패턴이다.

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FdhWF0z%2FbtrQSX0uhdb%2F1uHvC8FOqLDk0DuKyPfiK1%2Fimg.png)

```java
public interface Component {

    void execute();
}

public class ConcreteComponent implements Component {

    @Override
    public void execute() {
        System.out.println("Concrete");
    }
}


public class Decorator1 implements Component {

    private final Component component;

    public Decorator1(Component component) {
        this.component = component;
    }

    @Override
    public void execute() {
        System.out.println("Decorator 1");
        component.execute();
    }
}

public class Decorator2 implements Component {

    private final Component component;

    public Decorator2(Component component) {
        this.component = component;
    }

    @Override
    public void execute() {
        System.out.println("Decorator 2");
        component.execute();
    }
}

public class Main {

    public static void main(String[] args) {
        Component component = new ConcreteComponent();

        Decorator1 decorator1 = new Decorator1(component);
        Decorator2 decorator2 = new Decorator2(decorator1);
        decorator2.execute();
    }
}


```

위의 설계에서는 기존의 `ConcreteComponent`의 코드를 수정하지 않으면서 부가기능을 추가할 수 있도록 하였다. Decorator1,2 클래스는 Component를 합성관계로 가지면서 부가기능을 런타임에 추가할 수 있다.

## 믹스인
믹스인은 객체를 생성할 때 코드 일부를 클래스 안에 섞어넣어 재사용하는 기법이다. 상속과 유사해 보이지만, 상속이 클래스와 클래스 사이의 관계를 고정시키는 데 반해 유연하게 관계를 재구성 할 수 있어 상속과 같은 결합도 문제에서 자유롭다.

이터레이터를 추상화한 예제를 통해 믹스인을 이해해보자.

```scala
이터레이터
abstract class AbsIterator {
  type T
  def hasNext: Boolean
  def next(): T
}

//이터레이터가 반환하는 항목에 함수를 적용하는 메서드(foreach)를 정의
trait RichIterator extends AbsIterator {
  def foreach(f: T => Unit): Unit = { while (hasNext) f(next()) }
}

//문자열의 캐릭터를 차례로 반환하는 이터레이터
class StringIterator(s: String) extends AbsIterator {
  type T = Char
  private var i = 0
  def hasNext = i < s.length()
  def next() = { val ch = s charAt i; i += 1; ch }
}

//문자열의 각 문자를 출력하는 예제
object StringIteratorTest {
  def main(args: Array[String]): Unit = {
    class Iter extends StringIterator("Scala") with RichIterator
    val iter = new Iter
    iter foreach println
  }
}
```

위는 스칼라라는 언어를 통해 믹스인을 적용한 사례이다. 스칼라에서는 with 키워드를 통해 런타임에 동적으로 부가기능을 추가할 수 있다.

`class A extends Three with Four with Two` 와 같이 여러 믹스인을 쌓을 수 있으며, 이러한 특징을 쌓을수 있는 변경(Stackable Modification)이라고 한다.

> 스칼라에서는 여러 trait를 쌓아 올렸다 하여 stackable trait pattern이라고 부르기도 한다.

## Appendix

앞선 장에서 상속보다는 합성이 좋다라는 이야기를 많이 해왔는데 이번장에서는 구체적인 해결방법에 대해서 이야기 하고 있네요.
책에서는 디자인 패턴을 노골적으로 이야기 하지는 않았지만, 같이 디자인 패턴강의를 듣고 있고 마침 데코레이터 패턴도 배운 사례라 디자인 패턴으로 예시를 들어보았습니다!
시간이 된다면 데코레이터 패턴과 프록시 패턴의 차이 그리고 프록시에 대해서 따로 시간내어 정리하면 좋을것 같아요

**프록시(데코레이터 패턴 vs 프록시 패턴 정리)**
- https://waterfogsw.tistory.com/47

**스칼라 문법**
- https://docs.scala-lang.org/ko/tour/mixin-class-composition.html

**파이썬에서의 믹스인**
- https://hamait.tistory.com/859
