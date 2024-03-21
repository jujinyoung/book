# 20장 추상 클래스보다는 인터페이스를 우선하라
자바에서 제공하는 다중 구현 매커니즘은 <b>인터페이스와 추상 클래스</b> 두 가지 존재
> 책에서는 가능하면 인터페이스를 사용하라고 권장하고 있음

### 인터페이스의 장점
1. 유연성: 인터페이스는 다중 구현을 허용하기 떄문에 유연성을 향상
2. 진화: 기존에 발표된 인터페이스에 메소드를 추가하더라도, default메서드를 활용하면 기존 구현체를 깨트리지 않을 수 있다.
3. 구현의 분리: 인터페이스를 통해 구현을 캡슐화하고 클라이언트 코드가 구현 대신 인터페이스에만 의존하도록 할 수 있다.

### 추상 클래스의 장점
1. 상태 공유 및 관리: 구현 클래스들이 상태(필드)와 구현 코드를 공유할 수 있다.
2. 메소드 구현: 추상 메소드 뿐만 아니라, 구현된 메소드도 포함할 수 있다. (interface의 default)
> 자바 8부터는 default 메소드를 지원하기때문에 추상 클래스의 장점을 인터페이스에서도 가진다.

* 인터페이스: 다중 상속이 가능, 구현한 클래스와 같은 타입으로 취급
* 추상 클래스: 다중 상속이 불가능, 구현체와 상위-하위 클래스 관계를 갖는다.



### 클래스와 인터페이스를 동시에 상속 받을 경우
```
public class ParentClass {
    public void hello() {   //private으로 변경 시에도 에러
        System.out.println("Hello from ParentClass");
    }
}

public interface ParentInterface {
    default void hello() {
        System.out.println("Hello from ParentInterface");
    }
}

public class ChildClass extends ParentClass implements ParentInterface {
    public static void main(String[] args) {
        ChildClass childClass = new ChildClass();
        childClass.hello(); // "Hello from ParentClass" 출력
    }
}
```
* 만약 ParentInterface와 같은 ParentInterface2라는 인터페이스 두개를 상속하게 된다면 hello()를 반드시 재구현 (부모클래스 상속 안받을시)
* ParentClass의 hello를 private으로 변경할 경우도 클래스가 인터페이스보다 우선한다는 규칙때문에 에러가 발생한다. 
> 자바의 상속 규칙 중 하나는 <b>클래스가 인터페이스보다 우선한다.</b>