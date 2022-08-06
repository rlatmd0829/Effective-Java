# item 11. equals를 재정의하려거든 hashCode도 재정의하라

equals를 재정의한 클래스는 hashCode도 재정의 해야 한다.

그렇지 않으면 인스턴스를 HashMap이나 HashSet 같은 컬렉션의 원소로 사용할 때 문제가 발생한다.

## hashCode 일반 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면, hashCode도 변하면 안된다.
- equals가 두 객체가 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환한다.
- equals가 두 객체를 다르다고 판단했더라도, hashCode는 꼭 다를 필요는 없다.

## 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.

equals가 물리적으로 다른 두 객체를 논리적으로 같다고 할 때, hashCode는 서로 다른 값을 반환하다.

```java
Map<PhoneNumber, String> map = new HashMap<>();
map.put(new PhoneNumber(010,1234,5678), new Person("리치"));
```

이 코드에 map.get(new PhoneNumber(010,1234,5678))를 실행하면 "리치"가 아닌 null을 반환한다

PhoneNumber 클래스는 hashCode를 재정의하지 않았기 때문에, 논릴적 동치인 두 객체가 서로 다른 해시코드를 반환하여 get메서드는 엉뚱한 해시 버킷에 가서 객체를 찾으려 한 것이다.

HashMap은 해시코드가 서로 다른 엔트리끼리는 동치성 비교를 시도조차 않도록 최적화 되어있다.

## 동치인 모든 객체에서 똑같은 hashCode를 반환하는 코드

```java
@Override
public int hashCode() {
	return 42;
}
```
모든 객체에게 똑같은 값만 내어주므로 모든 객체가 해시테이블의 버킷 하나에 담겨 마치 연결리스트처럼 동작한다.

그 결과 평균 수행시간이 O(1)인 해시테이블이 O(n)으로 느려져서, 도저히 쓸 수 없게된다.

### 정리
equals를 재정의할 때는 hashCode도 반드시 재정의해야 한다. 그렇지 않으면 프로그램이 제대로 동작하지 않을 것이다.

재정의한 hashCode는 Object의 API 문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야 한다.
