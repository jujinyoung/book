# 22장 인터페이스는 타입을 정의하는 용도로만 사용하라
인터페이스는 자신을 구현한 클래스의 인터페이스를 참조할 수 있는 타입 역할을 한다.

### 인터페이스의 잘못된 예
```
public interface PhysicalConstants {
    static final double AVOGARDROS_NUMBER = 6.022_140_857e23;
    static final double BOTLZMANN_CONSTANT = 1.380_648_52e-23;
}
```
* 상수 인터페이스(메서드 없이 상수만 가득참)는 인터페이스를 잘못 사용한 안티패턴이다.
* 상수 인터페이스를 구현하는 것은 내부 구현을 클래스의 API로 노출하는 행위다.

### 상수 유틸리티 클래스
```
public class PhysicalConstants {
    private PhysicalConstants() {}  //인스턴스화 방지

    public static final double AVOGARDROS_NUMBER = 6.022_140_857e23;
    public static final double BOTLZMANN_CONSTANT = 1.380_648_52e-23;
}
```