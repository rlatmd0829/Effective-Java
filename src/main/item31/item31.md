# item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라

## 불공변인 제네릭 타입

서로 다른 Type1 Type2가 있을 때 `List<Type1>`은 `List<Type2>`의 하위 타입도 상위 타입도 아니다.

`List<Object>` 에는 어떤 객체든 넣을 수 있지만 `List<String>`에는 문자열만 넣을 수 있기 때문에 리스코프 치환 원칙에 어긋나 하위타입이 될 수 없다.

<br>

## 한정적 와일드카드 타입 - 생산자

### 와일드카드를 사용하지 않은 pushAll 메서드

```java
public void pushAll(Iterable<E> src) {
    for (E e : src) {
        push(E);
    }	
}
```

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

`Iterable<? extends E>`는 E의 iterable이 아니라 E의 하위 타입의 Iterable이어야 한다는 의미를 갖는다.

즉, E의 하위타입을 받겠다는 의미이다.

매개 변수 src는 stack이 사용할 E 인스턴스를 생산하므로 생산자라고 볼 수 있다.

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


```java
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...; 
numberStack.popAll(objects);
```

이번 상황역시 위와 비슷한 에러가 발생하는데, 이를 와일드카드 타입으로 해결이 가능하다.

### 와일드카드를 사용한 popAll 메서드

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty()) {
        dst.add(pop());
    }
}
```
위와 마찬가지로 E의 상위타입을 받겠다는 의미이다.

매개변수 dst는 Stack으로부터 E 인스턴스를 소비하므로 소비자라고 볼 수 있다.

<br>

## 펙스(PECS) : producer - extends, consumer - super

- 매개변수화 타입 T가 생산자(producer)라면 <? extends T>를 사용
- 매개변수화 타입 T가 소비자(consumer)라면 <? super T>를 사용

### item30-2 union 메서드에 와일드카드 적용

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    ...
}

public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
    ...
}
```

s1과 s2 E인스턴스를 생성하여 모두 생산자이니 PECS 공식에 따라 다음처럼 선언할 수 있다.

> 반환타입에는 한정적 와일드 카드 타입을 사용하면 안된다. 유연성을 높여주기는 커녕 클라이언트 코드에서 와일드카드 타입을 사용해야하기 때문이다.  
> 즉, 클래스 사용자가 와일드카드 타입을 신경써야 한다면 그 API에 무슨 문제가 있을 가능성이 크다.

### item30-7 max 메서드에 와일드카드 적용

```java
public static <E extends Comparable<E>> E max(List<E> list) {
    ...
}

public static <E extends Comparable<? super E>> E max(List<? extends E> list) {
    ...
}
```

- 입력 매개변수 : E 인스턴스를 생산하므로 원래의 `List<E>`를 `List<? extends E>`로 수정하였다.

- 타입 매개변수 : E가 `Comparable<E>`를 확장하는 개념이고 이때 Comparable은 언제나 소비자이므로 `Comparable<E>`는 E 인스턴스를 소비한다. 그래서 `Comparable<? super E>`로 수정할 수 있다.

<br>

## 타입 매개변수와 와일드카드

타입 매개변수와 와일드카드에는 공통되는 부분이 있어서, 메서드를 정의할 때 둘 중 어느 것을 사용해도 괜찮을 때가 많다.

### swap 메서드의 두가지 선언

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

public API라면 간단한 두 번째가 낫다. 어떤 리스트든 이 메서드에 넘기면 명시한 인덱스의 원소들을 교환해 줄 것이다. 신경써야할 타입 매개변수도 없다.

즉, 메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하면 된다.




