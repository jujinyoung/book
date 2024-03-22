# 23장 태그 달린 클래스보다는 클래스 계층구조를 활용하라
#### 태그달린 클래스: 객체가 여러 형태 중 하나임을 나타내기 위해 내부에 태그필드를 두고, 이 태그에 따라 동작이 달라지는 클래스

```
public class Figure {
    enum Shape {RECTANGLE, CIRCLE}

    //태그 필드 - 현재 모양을 나타낸다.
    final Shape shape;

    //사각형(RENCTANGLE)일 때만 쓰인다.
    double length;
    double width;

    //원(CIRCLE)일 때만 쓰인다.
    double radious;

    //원용 생성자
    public Figure(Shape shape, double radious) {
        this.shape = shape;
        this.radious = radious;
    }

    //사각형용 생성자
    public Figure(Shape shape, double length, double width) {
        this.shape = shape;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radious * radious);
            default:
                throw new AssertionError(shape);
        }
    }
}
```
* 두가지 이상의 의미를 표현할 수 있다. (원, 사각형)
#### 단점
1. 불필요한 코드
    * 태그에 따라 동작이 달라지므로, 크랠스 내부에는 사용되지 않는 필드가 있을 수 있으며, 이는 메모리 낭비를 초래한다.
2. 가독성 저하
    * 하나의 클래스에 여러 타입의 처리 로직이 혼재되어, 코드를 이해하기 어렵게 만듭니다.
3. 유지보수 어려움
    * 새로운 타입을 추가하거나 기존 타입을 수정할 때 모든 조건문을 검토하고 수정해야 한다. (area() 메서드)
4. 확장성 저하
    * 새로운 타입을 추가하기 위해서는 기존 코드를 수정해야 하므로, 개방-폐쇄 원칙(OCP)에 위배된다.
   
### 해결책
```
public abstract class Figure {
    abstract double area();
}

public class Circle extends Figure {

    final double radious;

    public Circle(double radious) {
        this.radious = radious;
    }

    @Override
    double area() {
        return Math.PI * (radious * radious);
    }
}

public class Rectangle extends Figure {

    final double length;
    final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    double area() {
        return length * width;
    }
}
```
* 추상 클래스를 정의하고, 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언하면 된다. 