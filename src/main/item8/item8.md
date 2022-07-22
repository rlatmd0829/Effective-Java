# item 8. finalizer와 cleaner 사용을 피하라

Finalizer 와 cleaner 는 예측할 수 없고, 느리고 일반적으로 불필요하다.

왜 사용되지 않는지 단점들을 알아보자.



### 즉시 수행된다는 보장이 없다.

finalizer와 cleaner는 즉시 수행된다는 보장이 없기 때문에, 제때 실행되어야 하는 작업은 절대 할 수 없다.



### 수행 여부조차 보장하지 않는다.

수행 여부조차 보장하지 않는다. 따라서 프로그램 생애주기와 상관없는, 상태를 영구적으로 수정하는 작업에서는 finalizer나 cleaner에 의존해서는 안된다.



### 성능문제

try-with-resources 로 자신을 닫도록 하여 가비지 컬렉터가 수거하는 것보다, finalizer와 cleaner을 사용한 객체를 생성하고 파괴하는것이 훨씬 더 비효율적이다.



### 보안문제

생성자나 직렬화 과정에서 예외가 발생하면 finalizer가 수행되는데, 이 finalizer를 오버라이딩한 하위클래스의 finalizer가 수행될 수 있다. 심지어, 이 finalizer를 static 필드에 할당하면 가비지 컬렉터에 의해 수거되지도 않는다.

-> 상속 자체를 막을 수 있는 final 클래스로 만들거나, A 클래스 안에서 아무 일도 하지 않는 final로 선언된 finalize메서드를 만든다.



