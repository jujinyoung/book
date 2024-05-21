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

### 로타입과 Object
List&lt;Object&gt;는 컴파일러에게 모든 타입을 허용한다는 의사를 전달한것이므로 제너릭에 완전히 발을 뺀 로타입 List와는 다르다.
```
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueof(42));    //Integer타입
    String s = strings.get(0);                  //컴파일러가 자동으로 String으로 형변환 -> Exception
}

private static void unsafeAdd(List list, Object o) {    //로타입 사용
    list.add(o);
}
```
* 로타입을 사용한 부분을 List<Object.>로 변경할경우 unsafeAdd부분에서 컴파일러가 예외를 던진다.

### 비한정적 와일드카드(?)
* 제너릭 타입을 쓰고 싶지만 실제 타입 매개변수가 무엇인지 신경 쓰고 싶지 않다면 물음표(?)를 사용
* 제너릭 타입 Set<E>의 비한정적 와일드카드 타입은 Set<?>이다. 이것은 어떤 타입이라도 담을 수 있는 가장 범용적인 매개변수화 Set 타입이다.
```
List<?> list = new ArrayList<>();
list.add(null);
list.add(1);    //오류
```
> 비한정적 와일드카드로 선언한 Collection<?>에는 어떤 원소도 넣을 수 없다. (null 외에는) 
> 매개변수화 타입을 Object로 지정하면 형변환, 검사를 통해 add를 허용한다.

### 로 타입을 사용하는 경우
> List.class, String[].class, int.class -> O <br>
> List&lt;String&gt;.class, List&lt;?&gt;.class -> X
* 자바 명세에는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했다.(배열과 기본타입만 허용)

```
if (o instanceof Set) {     // 로타입
    Set<?> s = (Set<?>) o   // 와일드카드 타입
}
```
* 로 타입이든 비한정적 와일드카드 타입이든 instanceof는 **똑같이 동작**한다. 비한정적 와일드카드 타입의 꺾쇠괄호와 물음표는 아무런 역할 없이 코드만 지저분하게 만든다.
* o타입이 Set임을 확인한다음 와일드카드인 Set<?>으로 변경해야 한다. -> 검사 형변환이므로 컴파일러 경고X