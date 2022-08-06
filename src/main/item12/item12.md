# item 12 toString을 항상 재정의하라

Object의 기본 toString은 `클래스이름@16진수` 해시코드를 반환하여 그다지 쓸모 없는 메시지를 출력하게 된다.

toString을 재정의 하면 훨씬 보기 편하게 만들 수 있다. 그리고 toString을 직접 호출하지 않아도 다른 어딘가에서 많이 호출하기 때문에 정의해두면 좋다.

실전에서 toString은 그 객체가 가진 주요 정보를 모두 반환하는게 좋다.

```java
@Override
public String toString() {
    return "PhoneNumber{" +
            "ariaCode=" + ariaCode +
            '}';
}
```

예외적으로 대부분의 열거 타입은 자바가 이미 완벽한 toString을 제공하므로 따로 재정의할 필요가 없다. 그러나 하위 클래스들이 공유할 문자열 표현이 있는 추상클래스는 toString을 재정의해준다.
