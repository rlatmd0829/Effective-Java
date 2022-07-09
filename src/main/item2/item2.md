# item 2. 생성자에 매개변수가 많다면 빌더패던을 고려하라

### 정적 팩터리와 생성자에 제약

정적 팩터리와 생성자에는 똑같은 제약이 하나있다. 선택적 매개변수가 많을때 적절히 대응하기 어렵다는 점이다.

매개변수가 20가지가 넘어가고 필수 매개변수와 선택 매개변수가 있을때 보통 점층적 생성자 패턴을 사용했었다.



### 점층적 생성자 패턴

필수 매개변수를 받는 생성자 1개, 그리고 선택 매개변수를 하나씩 늘려가며 생성자를 만드는 패턴

```java
public class NutritionFacts {
  private final int servingSize; // 필수
  private final int servings;    // 필수
  private final int calories;    // 선택
  private final int fat;         // 선택
  private final int sodium;      // 선택
  private final int carbohydrate;// 선택
  
  public NutritionFacts(int servingSize, int servings) {
    this(servingSize, servings, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories) {
    this(servingSize, servings, calories, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat) {
    this(servingSize, servings, calories, fat, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
    this(servingSize, servings, calories, fat, sodium, 0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
    this.servingSize = servingSize;
    this.servings = servings;
    this.calories = calories;
    this.fat = fat;
    this.sodium = sodium;
    this.carbohydrate = carbohydrate;
  }
}
```

점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다. 각 값의 의미가 무엇인지 헷갈릴 것이고, 매개변수가 몇개인지도 주의해서 세어 보아야 할 것이다.



### 자바 빈즈 패턴

이번에는 선택 매개변수가 많을 때 활용할 수 있는 두번째 대안인 자바빈즈 패턴을 보겠다.

매개변수가 없는 생성자로 객체를 만든 후, Setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식이다.

```java
public class NutritionFacts {
  // 매개변수들은 (기본값이 있다면) 기본값으로 초기화 된다.
  private int servingSize = -1; // 필수, 기본값 없음
  private int servings = -1;    // 필수, 기본값 없음
  private int calories = 0;    // 선택
  private int fat = 0;         // 선택
  private int sodium = 0;      // 선택
  private int carbohydrate = 0;// 선택
  
  public NutritionFacts() {}
  
  // 세터 메서드들
  public void setServingSize(int val) {servingSize = val;}
  public void setServings(int val) {servings = val;}
  public void setCalories(int val) {calories = val;}
  public void setFat(int val) {fat = val;}
  public void setSodium(int val) {sodium = val;}
  public void setCarbohydrate(int val) {carbohydrate = val;}
}
```

점층적 생성자 패턴의 단점들이 사라지고 더 읽기 쉬운 코드가 되었다.

하지만 자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

이처럼 일관성이 무너지는 문제 때문에 자바빈즈 패턴에서는 클래스를 분변으로 만들 수 없으며 스레드 안정성을 얻으려면 프로그래머가 추가 작업을 해줘야한다.



### 빌더 패턴

점층적 생성자 패턴의 안정성과 자바빈즈 패턴의 가독성을 겸비한 빌더 패턴을 알아보자.



빌더패턴이 동작하는 방식

1. 필수 매개변수만으로 생성자(혹은 정적 팩터리)를 호출해 빌더 객체를 얻는다.
2. 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정한다.
3. 마지막으로 매개변수가 없는 build 메서드를 호출해 객체를 얻는다.

```java
public class NutritionFacts {
  private int servingSize = -1; 
  private int servings = -1;    
  private int calories = 0;    
  private int fat = 0;         
  private int sodium = 0;      
  private int carbohydrate = 0;
  
  public static class Builder {
    // 필수 매개변수
    private final int servingSize;
    private final int servings;
    
    // 선택 매개변수
    private int calories = 0;    
    private int fat = 0;         
    private int sodium = 0;      
    private int carbohydrate = 0;
    
    public Builder(int servingSize, int servings) {
      this.servingSize = servingSize;
      this.servings = servings;
    }
    
    public Builder calories(int val) {
      calories = val;
      return this;
    }
    
    public Builder fat(int val) {
      fat = val;
      return this;
    }
    
    public Builder sodium(int val) {
      sodium = val;
      return this;
    }
    
    public Builder carbohydrate(int val) {
      carbohydrate = val;
      return this;
    }
    
    public NutritionFacts build() {
      return new NutritionFacts(this);
    }
  }
  
  private NutritionFacts(Builder builder) {
    servingSize = builder.servingSize;
    servings = builder.servings;
    calories = builder.calories;
    fat = builder.fat;
    sodium = bulder.sodium;
    carbohydrate = builder.carbohydrate;
  }
}
```

NutritionFacts 클래스는 불변이며, 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다. 이런 방식을 메서드 호출이 흐르듯 연결된다는 뜻으로 플루언트 API 혹은 메서드 연쇄라 한다.

다음은 이 클래스를 사용하는 클라이언트 코드의 모습이다.

```
NutritionFacts cocaCola = new NutritionFacts.Bulider(240, 8)
	.calories(100).sodium(35).carbohydrate(27).build();
```



빌더 패턴에도 징점만 있는것은 아니다.

객체를 만들려면, 그에 앞서 빌더부터 만들어야해서 매개변수가 4개이상은 되어야 값어치를 한다.

> lombok 을 사용하면 좀 더 편하게 builder 패턴을 사용할 수 있다.
