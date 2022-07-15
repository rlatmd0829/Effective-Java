# item 5. 자원을 직접 명시하지 말고 의존 객체주입을 사용하라.

사용하는 자원에 따라 동작이 달라지는 클래스는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

### 정적 유틸리티

```java
public class SpellChecker {
  private static final Lexicon dictionary = ...;
  
  private SpellChecker() {} // 객체 생성 방지
  
  public static boolean isValid(String word) { ... }
  public static List<String> suggestions(String typo) { ... }
}
```

### 싱글턴

```java
public class SpellChecker {
  private final Lexicon dictionary = ...;
  
  private SpellChecker(...) {}
  public static SpellChecker INSTANCE = new SpellChecker(...);
  
  public static boolean isValid(String word) { ... }
  public static List<String> suggestions(String typo) { ... }
}
```

위에 두 방식은 사용하는 자원에 따라 동작이 달라지는 클래스에는 적합하지 않다.

### 의존 객체 주입

```java
public class SpellChecker {
  private final Lexicon dictionary;
  
  private SpellChecker(Lexicon dictionary) {
    this.dictionary = Object.requireNonNull(dictionary);
  }
  
  public static boolean isValid(String word) { ... }
  public static List<String> suggestions(String typo) { ... }
}
```

의존 객체 주입이란 인스턴스를 생성할 때 필요한 자원을 넘겨주는 방식이다.

그리고 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있다.

의존 객체 주입을 사용하면 클래스의 유연성, 재사용성, 테스트 용이성을 개선할 수 있다.

### 의존 객체 주입 변형

의존 객체 주입의 변형으로, 생성자에 자원 팩터리를 넘겨주는 방식이 있다.

팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 말한다.

즉, 팩터리 메서드 패턴을 구현한 것이다.

```java
public class SpellChecker {
  private final Lexicon dictionary;
  
  private SpellChecker(DictionaryFactory dictionaryFactory) {
    this.dictionary = dictionaryFactory.get();
  }
  
  public static boolean isValid(String word) { ... }
  public static List<String> suggestions(String typo) { ... }
}
```

### java8 에 Supplier<T> 인터페이스가 팩터리를 표현한 완벽한 예이다.

```java
public class SpellChecker {
  private final Lexicon dictionary;
  
  private SpellChecker(Supplier<Dictionary> dictionarySupplier) {
    this.dictionary = dictionarySupplier.get();
  }
  
  public static boolean isValid(String word) { ... }
  public static List<String> suggestions(String typo) { ... }
}
```

```java
// 람다표현식 Supplier를 반환한다.
SpellChecker spellCheck = new SpellCheck(() -> new Dictionary);
```
