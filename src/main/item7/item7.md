# item 7. 다 쓴 객체 참조를 해제하라

### 메모리누수

자바는 가비지 컬렉터를 갖춘 언어로 다 쓴 객체를 알아서 회수해준다.

그렇다고 메모리 관리에 더 이상 신경을 쓰지않아도 되는건 아니다.

__어떤 객체에 대한 레퍼런스가 남아있다면 해당 객체는 가비지 컬렉션의 대상이 되지 않는다.__



스택을 간단히 구현한 다음 코드를 보자.

```java
public class Stack {
	private Object[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  
  public Stack() {
    elements = new Object[DEFAULT_INITIAL_CAPACITY];
  }
  
  public void push(Object e) {
    ensureCapacity();
    elements[size++] = e;
  }
	
	public Object pop() {
		if (size == 0) {
			throw new EmptyStackException();
		}
		return elements[--size];
	}
  
  /**
    * 원소를 위한 공간을 적어도 하나 이상 확보한다.
    * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
    */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

이 코드에서는 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는 문제가 발생하여 프로그램을 오래 실행하다 보면 점차 성능이 저하될 것이다.

pop() 메서드로 참조하는 size가 줄어들게 되면 앞으로는 다시 쓰이지 않게 되지만 가비지 컬렉터는 이것을 알아차리지 못하고 회수하지 않는다.

이것을 다 쓴 참조(obsolete reference)라 한다.



### 다 쓴 참조(obsolete reference) 해법

해법은 해당 참조를 다 썻을 때 null 처리(참조 해제)하면 된다.

스택 클래스에서는 각 원소의 참조가 더 이상 필요 없어지는 시점은 스택에서 꺼내질 때다.

다음은 pop 메서드를 제대로 구현한 모습이다.

```java
public Object pop() {
	if (size == 0) 
			throw new EmptyStackException();
	object result = elements[--size];
	elements[size] = null; // 다 쓴 참조 해제
	return result;
}
```

만약 null 처리한 참조를 실수로 사용하려 하면 프로그램은 즉시 NullPointException을 던지며 종료하여 프로그램 오류를 빠르게 잡을 수 있다.



### null 처리하는 일은 예외적이어야 한다.

그렇다고 필요 없는 객체를 볼 때마다 null 처리하면, 오히려 프로그램을 필요 이상으로 지저분하게 만든다.

필요없는 객체 레퍼런스를 정리하는 최선책은 그 레퍼런스를 가리키는 변수를 특정한 범위(scope)안에서만 사용하는 것이다. 변수의 범위를 가능한 최소가 되게 정의했다면(item 57) 이 일은 자연스럽게 이뤄진다.

-> 변수를 지역변수로 선언하여 범위를 벗어 난다면 자연스럽게 가비지 컬렉션에 대상이 되도록 하면 null 처리할 필요가 없다.

```java
public Object pop() {
	Object size = 10;
  ...
}
```

size는 scope이 pop() 안에서만 형성되어 있으므로 scope 밖으로 나가면 무의미한 레퍼런스 변수가 되기 때문에 GC에 의해 정리가 되어 굳이 null 처리 하지않아도 된다.



### 메모리 누수에 취약한 이유

그렇다면 null 처리는 언제 해야할까? stack 클래스는 왜 메모리 누수에 취약할까?

바로 스택이 자기 메모리를 직접 관리하기 때문이다.



이 스택은 elements 배열로 저장소 풀을 만들어 원소들을 관리한다.

배열의 활성 영역에 속한 원소들이 사용되고 비활성 영역은 쓰이지 않는다.



가비지 컬렉터가 보기에는 비활성 영역에서 참조하는 객체도 똑같이 유효한 객체다. 하지만 비활성 영역의 객체가 더 이상 쓸모없다는 건 프로그래머만 아는 사실이다.

그러므로 프로그래머는 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더는 쓰지 않을 것임을 가비지 컬렉터에 알려야한다.



일반적으로 자기 메모리를 직접관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야한다.



### 캐시 역시 메모리 누수를 일으키는 주범이다.

캐시를 사용할 때도 메모리 누수 문제를 조심해야 한다. 객체의 레퍼런스를 캐시에 넣어 놓고, 캐시를 비우는 것을 잊기 쉽다.

여러가지 해결책이 있지만, 캐시의 키에 대한 레퍼런스가 캐시 밖에서 필요 없어지면 해당 엔트리를 캐시에서 자동으로 비워주는 WeakHashMap을 쓸 수 있다.

> 캐시의 경우 시간이 지남에 따라 사용되지 않으면 그 가치를 떨어트리는 방법을 사용한다.



### WeakHashMap

HashMap 같은 경우는 어떤 객체가 null이 되어 버리면 해당 객체를 key로 하는 HashMap의 value 값도 더이상 꺼낼 일이 없는 경우가 발생하여 메모리를 차지한다.

하지만 캐시 값이 무의미해진다면 자동으로 처리해주는 WeakHashMap은 key 값을 모두 Weak 레퍼런스로 감싸 Strong reference가 없어진다면 GC의 대상이 된다.

> 주의할점 - WeakHashMap에 key로 String은 적합하지 못하다 String은 JVM에 의해 다른 곳에 store 되어 항상 strong reference로 남아있기 때문이다. (사용하고 싶으면 new로 생성)



### Java Reference 종류

자바는 효율적인 GC를 처리하기 위한 reference가 여러종류가 있어  개발자는 적절한 reference를 사용하여 GC에 의해 제거될 데이터 우선순위를 적용하여 좀 더 효율적인 메모리 관리를 할 수 있다.

Reference는 4가지 종류 `Strong Reference`,`Soft Reference `,`Weak Reference`, `Phantom Reference` 로 구분되어 있으며, 뒤로 갈수록 GC에 의해 제거될 우선순위가 높다.



- Strong Reference

우리가 자주 사용하는 객체 만들 때 모든 래퍼런스는 전부 Strong reference이다

```java
Integer prime = 1;
```

이 객체를 가리키는 강한 참조가 있는 객체는 GC대상이 되지않는다.



- Soft Reference

```java
Integer prime = 1;  
SoftReference<Integer> soft = new SoftReference<Integer>(prime); 
prime = null;
```

(최초 생성 시점에 이용 대상이 되었던 Strong Reference) 은 없고 대상을 참조하는 객체가 SoftReference만 존재할 경우 GC대상으로 들어가도록 JVM은 동작한다.  다만 WeakReference 와의 차이점은 메모리가 부족하지 않으면 굳이 GC하지 않는 점이다.



- Weak Reference

```java
Integer prime = 1;  
WeakReference<Integer> soft = new WeakReference<Integer>(prime); 
prime = null;
```

해당 객체를 가리키는 참조가 WeakReference 뿐일 경우 GC 대상이 된다.



- Phantom Reference

GC 과정은 여러 단계로 나뉜다.
GC 대상 객체를 찾는 작업, 객체를 처리(finalize), 메모리를 회수하는 작업

Soft Reference, Weak Reference는 GC 대상 객체를 찾는 작업에 개발자가 관여할 수 있게 해준다.

하지만 Phantom Reference는 finalize , 메모리를 회수하는 것에 관여한다.

GC가 객체를 처리하는 순서는 다음과 같다.

**Strong Ref > Soft Ref > Weak Ref > finalize > phantom Ref > 메모리 회수**

객체가 Strong,Soft, Weak 참조에 의해 참조되는지 판단하고 모두 아니면 finalize를 진행하고 phantom 여부를 판단한다.
따라서 finalize() 이후에 처리해야하는 리소스 정리 작업을 할 때 개발자가 관여할 수 있다.
하지만 거의 안쓰인다.



### 리스너, 콜백

클라이언트 코드가 콜백을 등록할 수 있는 API를 만들고 콜백을 뺼 수 있는 방법을 제공하지 않는다면, 계속해서 콜백이 쌓이기만 할 것이다. 이것 역시 `WeakHashMap`을 사용해서 콜백을 Weak 레퍼런스로 저장하면 GC가 이를 즉시 수거해 해결할 수 있다.

