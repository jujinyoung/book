# 10장 equals는 일반 규약을 지켜 재정의하라
### equals를 재정의 하지 말아야 하는 경우
1. 각 인스턴스가 본질적으로 고유하다.(ex.싱글턴, enum 등)
2. 인스턴스의 논리적 동치성을 검사할 일이 없다.
3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
4. 클래스가 private이거나 package-private이고 equals를 호출할 일이 없다.

### equals를 재정의 해야 하는 경우
객체 식별성(두 객체가 물리적으로 같은가, 동일성)이 아니라 논리적 동치성(동등성)을 확인해야 하는데 상위 클래스의 equals()가 이를 확인하도록 재정의되어 있지 않을 때는 필수로 equals()를 재정의해야 한다.

> equals는 재정의 하지 않을 수 있으면 안하는게 최선이다.


### equals 메서드의 일반적인 재정의 규약
* <b>반사성(reflexivity)</b>: A.equals(A) == true
* <b>대칭성(symmetry)</b>: A.equals(B) == B.equals(A)
* <b>추이성(transitivity)</b>: A.equals(B) && B.equals(C), A.equals(C)
* <b>일관성(consistency)</b>: A.equals(B) == A.equals(B)
* <b>null-아님</b>: A.equals(null) == false

#### 반사성: 객체는 자기자신과 같아야 한다.
```
public class CaseInsensitiveString {
    private final String s;

    public CaseInsensitiveString(String s) {
        this.s = Objects.requireNonNull(s);
    }

    //잘못된 케이스
    @Override
    public boolean equals(Object o) {
        if (o instanceof CaseInsensitiveString) {
            return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        }
        if (o instanceof String) {
            return s.equalsIgnoreCase((String) o);
        }
        return false;
    }
    
    //올바른 케이스
    //@Override
    //public boolean equals(Object o) {
    //    return o instanceof CaseInsensitiveString &&
    //            ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
    //}

    public static void main(String[] args) {
        CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
        String polish = "Polish";
        System.out.println(cis.equals(polish)); //true, false
        System.out.println(polish.equals(cis)); //false, false

        List<CaseInsensitiveString> list = new ArrayList<>();
        list.add(cis);

        System.out.println(list.contains(polish));  //false, false
    }
}
```
* CaseInsensitiveString의 객체의 경우 equals를 재정의 했기 때문에 일반 String과 비교해도 true를 반환
* 일반 String의 경우 CaseInsensitiveString의 존재를 모르기 때문에 false 반환 -> 대칭성 위배
* 올바른 케이스처럼 CaseInsensitiveString끼리만 비교하도록 equals를 정의해야한다.

#### 추이성: 1,2번 객체가 같고 2,3번 객체가 같다면 1,3번 객체도 같아야 한다.
```
public class ColorPoint extends Point{
    private final Color color;
    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }
    
    //대칭성 위반: Point와 ColorPoint비교시 Point.equals는 true 반대는 false가 나옴
    //@Override
    //public boolean equals(Object o) {
    //    if (!(o instanceof ColorPoint)) {
    //        return false;
    //    }
    //    return super.equals(o) && ((ColorPoint) o).color == color;
    //}

    //추이성 위반
    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point)) {
            return false;
        }
        // o가 일반 Point면 색상을 무시하고 비교한다.
        if (!(o instanceof ColorPoint)) {
            return o.equals(this);
        }

        return super.equals(o) && ((ColorPoint) o).color == color;
    }

    public static void main(String[] args) {
        ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
        Point p2 = new Point(1, 2);
        ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);
        System.out.println(p1.equals(p2));  //true
        System.out.println(p2.equals(p3));  //true
        System.out.println(p1.equals(p3));  //false
    }
}
```
* equals를 비교객체가 ColorPoint가 아닐경우 무시하고 ColorPoint일 경우 색상까지 비교한다면 (1,2), (2,3) 객체는 true지만 (1,3) 객체는 색상까지 비교하므로 false를 반환한다. -> 추이성 위배 
#### 추이성을 getClass()로 해결시 문제 사항
```
@Override
public boolean equals(Object o) {
    if (o == null || o.getClass() != getClass()) {
        return false;
    }
    Point p = (Point) o;
    return p.x == x && p.y == y;
}

@Override
public int hashCode() {
    return 31 * x + y;
}
```
* 해당 방법대로 getClass()로 Point 클래스만 비교(추이성 해결)해서 eqauls를 반환하면 상속관계를 무시하고 같은 Point 클래스 관계에서만 비교 수행
-> 리스코프 치환 원칙 위배 
> <b>상위 클래스(Point)를 상속받아 구체 클래스(ColorPoint)를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재 X<b/>
#### 추이성 해결방법 (상속대신 컴포지션을 사용)
```
public class ColorPointSolved {
    private final Point point;
    private final Color color;
    
    public ColorPointSolved(Point point, Color color) {
        this.point = point;
        this.color = Objects.requireNonNull(color);
    }

    //일반 Point를 반환하는 뷰
    public Point asPoint() {
        return point;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof ColorPointSolved)) {
            return false;
        }
        ColorPointSolved cp = (ColorPointSolved) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }
}
```
* Point의 equals를 instanceof기반으로 구현
* Point를 상속하는 방법 대신 Point를 colorPointSolved의 private 필드로 두고 asPoint()로 Point를 제공

#### 일관성: 두 객체가 같다면 앞으로도 영원히 같아야 한다.
* equals의 판단에 신뢰할 수 없는 자원이 끼어들어서는 안된다.

#### null-아님
```
@Override public boolean equals(Object o) {
    //이 방법은 불필요하다
    //if (o == null) {
    //    return false;
    //}
    
    //instanceof 연산자로 입력 매개변수 타입 체크    
    if (!(o instanceof MyType)) return false;
    ...
}
```