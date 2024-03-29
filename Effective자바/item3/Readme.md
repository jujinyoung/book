# 3장 생성자나 열거 타입으로 싱글턴임을 보증하라
### 1. private 생성자 + public static final 필드
```
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    
    private Elvis() {}
}
```
**장점**<br>
간결하고 싱글턴임을 api에 들어낼 수 있다. <br>
<br>
**단점**<br>
1. 싱글톤을 사용하는 클라이언트 테스트하기 어려워진다. 
* 인터페이스가 구현되어 있다면 mocking해서 가짜 객체를 주입받아 테스트하면 된다.
2. 리플렉션으로 private 생성자를 호출할 수 있다. 
```
Constructor<Elvis> defaultConstructor = Elvis.class.getDeclaredConstructor();
defaultConstructor.setAccessible(true);
Elvis elvis1 = defaultConstructor.newInstance();
Elvis elvis2 = defaultConstructor.newInstance();
System.out.println(elvis1 == elvis2);
```
> false

private 생성자로 객체 생성을 막았지만 리플렉션에 의해 풀려버렸다.

3. 역직렬화 할 때 새로운 인스턴스가 생길 수 있다.
```
private Object readResolve() {
    return INSTANCE;
}   
```
역직렬화시 해당코드를 추가하면 같은 객체임을 보장 

#### 코드
```
public class Elvis implements IElvis, Serializable {

    public static final Elvis INSTANCE = new Elvis();
    private static boolean created;

    private Elvis() {
        //리플렉션 시 최초 생성된 이후로 private 생성자 호출 막기
        if (created) {
            throw new UnsupportedOperationException("can't be created by constructor.");
        }

        created = true;
    }

    public void leaveTheBuilding() {
        System.out.println("Whoa baby, I'm outta here!");
    }

    public void sing() {
        System.out.println("I'll have a blue~ Christmas without you~");
    }

    //역직렬화 시 같은 객체 보장
    private Object readResolve() {
        return INSTANCE;
    }
}
```

### 2. private 생성자 + 정적 팩터리 메서드
```
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elivis getInstance() { return INSTANCE; }
}
```
**단점**<br>
1번예시와 동일함<br>

**장점**<br>
1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
* getInstance 호출부에서 INSTANCE를 return 하지 않고 new Elvis()를 리턴하면 다른 인스턴스를 넘길 수 있다.

2. 정적 팩터리를 제너릭 싱글턴 팩터리로 만들 수 있다.
```
public class MetaElvis<T> {

    private static final MetaElvis<Object> INSTANCE = new MetaElvis<>();
    private MetaElvis() {}
    public static <T> MetaElvis<T> getInstance() { return (MetaElvis<T>) INSTANCE; }

    public static void main(String[] args) {
        MetaElvis<String> elvis1 = MetaElvis.getInstance();
        MetaElvis<Integer> elvis2 = MetaElvis.getInstance();
        System.out.println(elvis1.equals(elvis2));
    }
}
```
3. 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다. 
```
Supplier<Elvis> elvisSupplier = Elvis::getInstance;
```
* 생성자는 불가능하지만 메서드로 제공하기 때문에 가능

### 3. 열거 타입 방식의 싱글턴 - 바람직한 방법
```
public enum Elvis {
    INSTANCE;
    
    public void leaveTheBuilding() {}
}
```
* 기존의 단점인 리플렉션시 생성자를 호출하는 문제, 역직렬화 문제를 모두 해결할 수 있다.