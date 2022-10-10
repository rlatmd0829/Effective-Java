# item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

## 불공변인 매개변수화 타입

서로 다른 Type1 Type2가 있을 때 List<Type1>은 List<Type2>의 하위 타입도 상위 타입도 아니다.

List<Object> 에는 어떤 객체든 넣을 수 있지만 List<String>에는 문자열만 넣을 수 있기 때문에 리스코프 치환 원칙에 어긋나 하위타입이 될 수 없다.

## 한정적 와일드카드 타입 - 생산자

### 와일드카드를 사용하지 않은 pushAll 메서드

```java
public void pushAll(Iterable<E> src) {
    for (E e : src) {
        push(E);
    }	
}
```

src 매개 변수는 stack이 사용할 E 인스턴스를 생산하므로 생산자라고 볼 수 있다.

```java
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> iterable = ...;
numberStack.pushAll(iterable);
```

Integer는 Number의 하위 타입이므로 논리적으로 잘 동작해야 할 것 같지만 실제로는 타입 변경할 수 없다는 에러가 발생한다.

이런 상황에 한정적 와일드카드 타입을 사용하면 된다.

### 와일드카드를 사용한 pushAll 메서드

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) {
        push(E);
    }
}
```

Iterable <? extends E>는 E의 iterable이 아니라 E의 하위 타입의 Iterable이어야 한다는 의미를 갖는다.

<br>

## 한정적 와일드카드 타입 - 소비자

### 와일드카드를 사용하지 않은 popAll 메서드

```java
public void popAll(Collection<E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

dst 매개변수는 Stack으로부터 E 인스턴스를 소비하므로 소비자라고 볼 수 있다.

```java
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...; 
numberStack.popAll(objects);
```

Collection<Object>는 Collections<Number>의 하위 타입이 아니기 때문에 오류가 발생한다. 이번 상황역시 와일드카드 타입으로 해결이 가능하다.

### 와일드카드를 사용한 popAll 메서드

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```

<br>

## 펙스(PECS) : producer - extends, consumer - super

- 매개변수화 타입 T가 생산자(producer)라면 <? extends T>를 사용
- 매개변수화 타입 T가 소비자(consumer)라면 <? super T>를 사용

반환타입에는 한정적 와일드 카드 타입을 사용하면 안된다. 유연성을 높여주기는 커녕 클라이언트 코드에서 와일드카드 타입을 사용해야하기 때문이다.

즉, 클래스 사용자가 와일드카드 타입을 신경써야 한다면 그 API에 무슨 문제가 있을 가능성이 크다.

