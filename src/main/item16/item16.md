# item 16. public 클래스에서는 public 필드가 아닌 다른 접근자 메서드를 사용하라

개발을 하다보면 인스턴스 필드들만 모아놓는 목적만 가진 아래와 같은 클래스를 구성하는 경우가 있다.

```java
class Point {
	public double x;
	public double y;
}
```

이러한 클래스 형태로 구현하였을 때는 클라이언트에서 필드에 직접 접근할 수 있으므로 캡슐화의 이점을 제공하지 못한다.

그리고 또 다른 단점들은

- API를 수정하지 않고는 내부 표현을 바꿀 수 없다.
  - public 필드로만 구성되어 있기 때문에 내부 표현을 변경하기 위해서는 필드를 변경해야 한다.
- 불변식을 보장할 수 없다.
  - 클라이언트에서 직접적으로 필드에 접근하고 있으므로 클라이언트에 의해 언제든지 변경이 가능하다.
- 외부에서 필드에 접근 할 때 부수적인 로직을 추가할 수 없다.
  - Point.x 라는 필드를 조회했을 때 부수적인 로직을 추가할수가 없다. (메서드가 존재하면 그 안에서 부수적인 로직 추가 가능)

## 필드를 private로 바꾸고 public 접근자(getter) 사용

접근자와 변경자 메서드를 활용해 데이터를 캡슐화한다.

```java
class Point {
	private double x;
	private double y;
	
	public Point(double x, double y) {
		this.x = x;
		this.y = y;
    }
	
	public double getX() { return x; }
	public double getY() { return y; }
	
	public void setX(double x) { return this.x = x; }
	public void setY(double y) { return this.y = y; }
}
```

이 처럼 구현하게 되면, 클래스의 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 제공하게 된다.

이 전 처럼, 필드를 공개하면 이를 사용하는 클라이언트가 생겨날 것이므로 내부 표현 방식을 마음대로 바꿀 수 없게 된다.

## package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.

- 클래스 선언 면에서나 이를 사용하는 코드 면에서 접근자 방식 보다 훨씬 깔끔하다.
  - 데이터 필드가 노출되어 getter, setter 없이 사용하여 코드가 깔끔해진다.
- 클라이언트 코드가 이 클래스 내부 표현에 묶이기는 하나, 클라이언트도 어차피 이 클래스를 포함하는 패키지 안에서만 동작하는 코드일 뿐이다.
  - 데이터 필드명을 변경해도 패키지 외부에 영향없이 내부만 바꾸면 되기 때문에 괜찮다.
- private 중첩 클래스의 경우라면 수정 범위가 더 좁아져서 괜찮다.

그 이유는 같은 패키지 안에서 어떤 특정 이유 때문에 사용하던가 탑 레벨 클래스에서만 접근하기 때문에 위에서 언급한 단점들이 나타날 이유가 없어 문제될 것이 없다.

## 자바 라이브러리에서 public 클래스의 필드를 직접 노출하지 말라는 규칙을 어기는 사례

대표적인 예로 `java.awt.package` 패키지의 Point와 Dimension 클래스가 있다.

public으로 공개된 멤버를 가지고 있는 Dimension 클래스는 언제든 변경될 수 있으므로 보통 방어적 복사가 이용되고 이는 성능 문제를 일으킨다.

```java
public Dimension getSize() {
    return new Dimension(width, height);
}
```

## 핵심정리

public 클래스는 절대 가변 필드를 직접 노출해서는 안된다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다.

하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 노출하는 편이 나을때도 있다.
