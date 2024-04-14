# 26장 로 타입은 사용하지 마라

### 로 타입(Raw Type)이란?
* 제너릭 도입 이전의 코드와 호환성을 위해 존재, 제너릭 클래스 또는 인터페이스 이름 뒤에 타입 매개변수를 지정하지 않고 사용하는 것
* ex) List 인터페이스의 로 타입은 List이며, List<E>의 제너릭 타입에서 E를 생략한 것이다.

#### 컬렉션의 로 타입 - 잘못된 예시
```
//stamp 인스턴스만 취급한다.
private final Collection stamps = ...;

stamps.add(new Coin(...));  //unchecked call 경고를 내뱉는다.

for ( Iterator i = stamps.iterator(); i.hasNext(); ) {
    Stamp stamp = (Stamp) i.next();  // ClassCastException을 던진다.
    stamp.cancel();
}
```
* 해당 코드는 컴파일 오류를 발생하지 않지만 Stamp객체를 꺼내려는 순간 실행중에 오류(Runtime Exception)가 발생할 수 있다.

#### 제너릭 지원 이후
```
private final Collection<Stamp> stamps = ...;
stamps.add(new Coin()); //컴파일 오류 발생
```
* 제너릭 사용 이후에는 stamps에는 Stamp의 인스턴스만 넣어야 함을 컴파일러가 인지하게 된다. 
> **로 타입을 쓰면 제너릭이 안겨주는 안전성과 표현력을 모두 잃게 된다.**

### List와 List<Object>