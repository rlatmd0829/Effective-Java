# item 10. equals는 일반 규약을 지켜 재정의 하라

equals는 재정의하기 쉬워보이지만 곳곳에 함정이 있다. 문제를 회피하는 가장 쉬운 길은 아예 재정의하지 않는 것이다.

그러니 다음에 해당된다면 재정의하지 않는것이 좋다.

## equals를 재정의 하면 안되는 경우

### 각 인스턴스가 본질적으로 고유할 때
값 클래스(Integer, String)가 아닌 동작하는 개체를 표현하는 클래스 ex) Thread

### 인스턴스의 '논리적 동치성'을 검사할 일이 없을 때
객체값 비교가 아니라 객체 안에 값을 검사할 일이 없을 경우

### 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞을 때
Set은 AbstractSet이 구현한 equals를 상속, List는 AbstractList, Map은 AbstractMap

### 클래스가 private이나 package-private이고 equals를 호출할 일이 없을 때
```java
// equals가 실수로 호출되는 걸 막고 싶다면
@Override public boolean equals (Object o){
  throw new AssertionError();
}
```

## equals를 재정의 해야하는 경우

객체 식별성(두 객체가 물리적으로 같은가)이 아닌 논리적 동치성을 확인해야 하는데,
상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의 되지 않았을 때

ex) 두 값 객체(Integer, String)를 equals로 비교하는 경우, 객체가 같은지가 아니라 값이 같은지를 알고싶을 것이다.
equals가 논리적 동치성을 확인하도록 재정의 하면 값비교 가능하다.

## equals 메서드 재정의 일반 규약 - 동치관계

동치 클래스 : 집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산

-> equals 메서드가 쓸모 있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 교환이 가능해야 한다.

### 반사성(reflexivity)
null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.

### 대칭성(symmetry)
null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)가 true면 y.equals(x)도 true다.

### 추이성(transitivity)
null이 아닌 모든 참조 값 x,y,z에 대해, x.equals(y)가 true이고, y.equals(z)도 true면 x.equals(z)도 true다.

### 일관성(consistency)
null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)를 반복해서 호출하면 항상 true이거나 false다.

### null-아님
null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.

## 정리
꼭 필요한 경우가 아니면 equals를 재정의 하지 않는게 좋다.

많은 경우에 Object의 equals가 원하는 비교를 정확히 수행해준다.

그래도 재정의 해야할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯가지 규약을 지켜가며 비교해야한다.
