# item 6. 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많다.

### 문자열

자바의 String은 String Pool 에서 관리되어 동일한 문자열은 하나로 관리된다. 

하지만 new 로 String을 생성하게 되면 동일한 문자열 이라도 새로 인스턴스를 생성하기 때문에 불필요한 객체를 생성하는 것이다.

```java
// 이 방법은 좋지않다.
String s = new String("test");

// 이 방법 사용
String s = "test";
```

### 정규식, Pattern

```java
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"
        + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");	
}
```

이 코드는 String.matches를 사용할 때 마다 정규표현식을 사용하고 버려져서 곧바로 가비지 컬렉션에 대상이 된다.

그래서 정규표현식을 표현하는 불변인 Pattern 인스턴스를 클래스 초기화 과정에서 직접 생성해 캐싱해두고, 나중에 메서드가 호출될 때마다 이 인스턴스를 사용하게 해야한다.

```java
public static final Pattern ROMAN = Pattern.compile(
	"^(?=.)M*(C[MD]|D?C{0,3})"
	+ "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String s) {
		return ROMAN.matcher(s).matches();
    }
```

### 오토박싱

```java
private static long sum() {
	Long sum = 0L;
	for (long i = 0; i <= Integer.MAX_VALUE; i++) {
		sum += i;
    }
	return sum;
}
```

이 코드는 sum을 Long으로 선언하여 for문을 진행하면서 long 타입인 i가 Long 타입인 sum에 더해질 때마다 오토박싱이 일어나 성능에 영향을 미친다.

단순히 Long 타입인 sum을 long 타입으로 바꿔주면 성능이 개선된다.
