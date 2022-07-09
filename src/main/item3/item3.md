# item 3. 생성자나 열거 타입으로 싱글턴임을 보증하라



## 싱글턴이란?

싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

그런데 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다. 왜냐하면 타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문이다.

다음으로 싱글턴임을 보증하는 3가지 방법을 알아보겠다.

<br>

## 1. private 생성자 + public static final 필드

```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  
  public void leaveTheBuilding() { ... }
}
```

private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한번만 호출된다.

하지만 리플렉션을 통해 private 생성자를 호출하여 싱글턴이 깨질 수 있으므로 flag를 걸어 두번째 객체가 생성되려 할때 예외를 던지게 하면된다.

```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private static boolean flag;
  private Elvis() {
  	if (flag) {
      throw new ...
    }
    flag = true;
  }
  
  public void leaveTheBuilding() { ... }
}
```



<br>

## 2. private 생성자 + 정적팩터리 메서드 + private static final 필드

```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis() { ... }
  public static Elvis getInstance() { return INSTANCE; }
  
  public void leaveTheBuilding() { ... }
}
```

Elvis.getInstance 는 항상 같은 객체의 참조를 반환하므로 제2의 인스턴스란 결코 만들어지지 않는다. (역시 리플렉션을 통한 예외는 똑같이 적용된다.)

### 정적 팩터리 메서드 장점

- 클라이언트 코드를 변경하지 않고 팩터리 메서드를 변경하여 싱글톤이 아니게 변경할 수 있다.

- 정적 팩토리를 제네릭 싱글턴 팩토리로 만들 수 있다는 점이다.

  타입은 다르게 싱글톤 객체로 만들 수 있다.

- 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다는 점이다.



__그러나 위에 두 방식은 직렬화 역직렬화를 거치게 되면 싱글턴이 깨지게 되는 문제가 있다.__

그래서 직렬화를 할 때 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다.

readResolve 라는 메서드 이름으로 만들게 되면 역직렬화 과정에서 이 메서드를 호출하게 설계가 되어있어 이 메서드까지 선언해줘야 직렬화 역직렬화 과정에서도 싱글턴을 유지할 수 있다.

```java
private Object readResolve() {
	return INSTANCE;
}
```



<br>

## 3. 원소가 하나인 열거타입

```java
public enum Elvis {
	INSTANSE;
  
  public void leaveTheBulding() { ... }
}
```

직렬화도 쉽게 가능하고, 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.

3가지 방법중 싱글턴을 만드는 가장 좋은방법이다.
