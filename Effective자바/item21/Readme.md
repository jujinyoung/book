# 21장 인터페이스는 구현하는 쪽을 생각해 설계하라
> 주로 인터페이스의 default메서드를 추가할때의 문제점에 대해 다루고 있음

### 디폴트메서드를 추가하는건 조심해야한다.
* 자바 8전에는 기존 구현체를 깨뜨리지 않고 인터페이스에 메서드를 추가할 방법은 존재하지 않았다. default 메서드의 등장 이후 메서드를 추가할 수 있게 되었다.

### 디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.
```
public abstract class ParentClass implements ParentInterface{
    public void hello() {
        System.out.println("abst hello!");
    }
}

public interface ParentInterface {
    default void hello(){
        System.out.println("hi!!");
    }
}
```
* 다른목적으로 사용되는 구현체의 hello()가 인터페이스의 hello()보다 우선하기 때문에 인터페이스의 hello를 호출하려는 클라이언트가 의도치 않은 동작 가능성이 있다.

### 인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야 한다.