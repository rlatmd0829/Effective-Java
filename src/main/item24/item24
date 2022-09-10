# item 24. 멤버 클래스는 되도록 static으로 만들어라

## 중첩클래스
다른 클래스 안에 정의된 클래스를 말한다.
- 중첩 클래스는 자신을 감싼 바깥 클래스에서만 사용되어야 한다.
- 중첩 클래스를 사용함으로써 불필요한 노출을 줄여 캡슐화를 할 수 있고 가독성 좋고 유지보수하기 좋은 코드를 작성할 수 있다.


### 중첩 클래스 종류
- 정적 멤버 클래스
- 비정적 멤버 클래스
- 익명 클래스
- 지역 클래스

## 정적 멤버 클래스
정적 멤버 클래스와 비정적 멤버 클래스를 구분하는 기준은 static 키워드가 함께 작성되었는지 여부로 판단할 수 있다.

```java
class Outer { 
    private int out = 100;
    static class Inner { 
        private int in;
		
        void callOut() {
            Outer outer = new Outer();
            System.out.println(outer.out);
        }
    }
}
```
- 정적 내부 클래스는 외부 클래스의 private 멤버에도 접근할 수 있다.
- 개념상 중첩 클래스의 인스턴스가 바깥 인스턴스와 독립적으로 존재 할 수 있다면 정적 멤버 클래스로 만들어야 한다.

```java
// 정적 멤버클래스 인스턴스 생성
Outer.Inner inner = new Inner();
```

## 비정적 멤버 클래스
static 키워드 없는 내부 클래스이다.

```java
class Outer {
    private int out = 100;

    class Inner {
        private int in;
    }
}
```
- 비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다.
- 그러므로 비정적 멤버 클래스는 this 키워드로 바깥 인스턴스의 메서드를 호출하거나 참조를 가져올 수 있다.

```java
class Outer {
    private int out = 100;

    public void create(){
        ...
    }

    class Inner {
       private int in;

       void accessOuter(){
           Outer.this.create();
       }
    }
}
```

```java
// 비정적 멤버클래스 인스턴스 생성
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

### 비정적 멤버 클래스 활용
비정적 멤버 클래스는 어댑터를 정의할 때 자주 사용된다.

즉, 어떤 클래스의 인스턴스를 감싸 마치 다른 클래스의 인스턴스처럼 보이게 하는 뷰로 사용하는 것이다.

예컨대 Map 인터페이스의 구현체들은 보통(KeySet, entrySet, values 메서드가 반환하는) 자신의 컬렉션 뷰를 구현할 때 비정적 멤버 클래스를 사용한다.

비슷하게, Set과 List 같은 다른 컬렉션 인터페이스 구현들도 자신의 반복자를 구현할 때 비정적 멤버 클래스를 주로 사용한다.

```java
public class MySet<E> extends AbstractSet {
    @Override
    public Iterator iterator() {
        return new MyIterator();
    }

    private class MyIterator implements Iterator<E>{
        ...
    }
}
```

### 멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.

static을 생략하면 바깥 인스턴스로의 숨은 외부 참조를 갖게된다. 이 참조를 저장하려면 시간과 공간이 소비된다.

더 심각한 문제는 가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못하도록 하는 메모리 누수가 생길 수도 있다.

따라서 멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 static을 붙이는게 좋다.

## 익명 클래스
- 이름이 없다.
- 바깥 클래스의 멤버가 아니다.
- 코드의 어디서든 만들 수 있다.

```java
public class TestClass {
    Integer intInstance = 10;
    
    void doX() {
        new SInterface() {
            @Override
            public void doSometing() {
                //바깥 인스턴스 참조
                System.out.println(intInstance);
            }
        };
    }
}
    
    interface SInterface {
    void doSometing();
}
```

## 지역 클래스
static 멤버는 갖지 못하며, 클래스 내부에서 필요한 기능을 정의할 때 사용한다.
- 네가지 중첩 클래스중 가장 드물게 사용된다.
- 지역 변수를 선언할 수 있는 곳이면 어디서든 선언이 가능하다.
- 유효 범위도 지역변수와 같다.

```java
public class TestClass {

    void x() {
        class LocalClass {
            void doPrint() {
                System.out.println("LocalClass");
            }
        }
    }   
}
```

---

### Reference
- [alkwen0996, 이펙티브 자바 item24](https://velog.io/@alkwen0996/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C24-%EB%A9%A4%EB%B2%84-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%EB%90%98%EB%8F%84%EB%A1%9D-static%EC%9C%BC%EB%A1%9C-%EB%A7%8C%EB%93%A4%EC%96%B4%EB%9D%BC)
- [민동현, 멤버 클래스는 되도록 static으로 만들자](https://donghyeon.dev/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%EC%9E%90%EB%B0%94/2021/03/13/%EB%A9%A4%EB%B2%84-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-%EB%90%98%EB%8F%84%EB%A1%9D-static%EC%9C%BC%EB%A1%9C-%EB%A7%8C%EB%93%A4%EC%9E%90/)
