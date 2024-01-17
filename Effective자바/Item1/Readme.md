# 1장 생성자 대신 정적 팩토리 메서드를 고려하라
### 장점
#### 1. 이름을 가질 수 있다.
```
public class Order {
    private boolean prime;
    private boolean urgent;
    private Product product;
    
    public BookTest(boolean prime, Product product) {
        this.prime = prime;
        this.product = product;
    }
    //컴파일 에러
    public BookTest(boolean urgent, Product product) {
        this.urgent = urgent;
        this.product = product;
    }
}
```
* 만약 PrimeOrder의 생성자와 BookOrder의 생성자를 만들고 싶을 때 같은 타입의 파라미터를 사용하면 위에 코드처럼 컴파일 에러가 난다.
```
public class Order {

    private boolean prime;
    private boolean urgent;
    private Product product;

    public static Order primeOrder(Product product) {
        Order order = new Order();
        order.prime = true;
        order.product = product;
        return order;
    }

    public static Order urgentOrder(Product product) {
        Order order = new Order();
        order.urgent = true;
        order.product = product;
        return order;
    }
}
```
* primeOrder와 urgentOrder라는 정적 팩터리 메서드를 사용하면 컴파일 에러가 나지 않고 이름으로 어떠한 기능을 가지는지 유추 가능
> 생성자에 넘기는 매개변수와 생성자 자체만으로는 어떠한 기능을 하는지 유추하기가 힘듬, 정적 팩터리를 사용해서 이름을 잘 지으면 반환되는 **객체의 특성**을 쉽게 묘사할 수 있다.

#### 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
```
private Order(){}

private static final Order ORDER = new Order();

public static Order newInstance() {
    return ORDER;
}
```
* 위와 같은 방식으로 객체를 클래스내부에서 생성하고 관리함으로써 불필요한 객체 생성을 피할 수 있다.(싱글턴)
#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
```
//인터페이스
public interface ItemInterface {

    String print(String value);

    //자바 8 이후로 인터페이스도 정적 메서드를 가질 수 있음 
    static ItemInterface of(String Yn) {
        if ("Y".equals(Yn)) {
            return new Item1();
        } else {
            return new Item2();
        }
    }
}

//구현클래스1
public class Item1 implements ItemInterface{
    @Override
    public String print(String value) {
        return "Item1 = " + value;
    }
}

//구현클래스2
public class Item2 implements ItemInterface{
    @Override
    public String print(String value) {
        return "Item2 = " + value;
    }
}


public class ItemTest {

    public static void main(String[] args) {
        ItemInterface hi = ItemInterface.of("Y");
        System.out.println(hi.print("hi"));
    }
}
```
> Item1 = hi
* ItemInterface의 of 메서드의 파라미터인 Yn에 따라 다른 클래스의 객체를 반환한다.(4번)
* 해당 클래스의 객체들은 ItemInterface의 하위 타입임(3번)
* 현재 사용하는 main에서는 **Item1, Item2 클래스의 존재를 몰라도** ItemInterface에서 전달해주는 객체의 print를 수행한다. 

#### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
```
private static void printHello() {
    ServiceLoader<HelloService> loader = ServiceLoader.load(HelloService.class);
    Optional<HelloService> helloServiceOptional = loader.findFirst();
    helloServiceOptional.ifPresent(h -> {
        System.out.println(h.hello());
    });
}
```
printHello 메서드가 호출되기 전까지 HelloService의 구현체가 존재하지 않는다. 
### 단점
#### 1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩토리 메서드만 제공하면 하위 클래스를 만들 수 없다.
* 싱글턴의 경우 이 단점에 해당됨. 
* 생성자를 public으로 열고 정적 팩터리 메서드도 제공하는 경우도 있다.
* ex) List는 new ArrayList<>() 와 List.of(...) 으로 생성이 가능하다.

#### 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
Javadoc에서 설명이 명확히 드러나지 않는다. -> 메서드 이름을 알려진 규악에 따라 짓는 식으로 문제 완화

### 정적 팩터리 메서드의 명명 방식
* from: 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
> Date d = Date.from(instant);
* of: 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
> Set<<d>Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
* valueOf: from과 of의 더 자세한 버전
> BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
* instance 혹은 getInstance: (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
> 싱글턴 예시 참조
* getType: 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. (Type은 반환할 객체 타입)
> FileStore fs = Files.getFileStore(path);

등등















