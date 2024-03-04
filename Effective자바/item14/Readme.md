# 14장 Comparable을 구현할지 고려하라
Comparable을 구현했다는 것은 그 클래스의 인스턴스들은 자연적인 순서(natural order)가 있음을 뜻한다.
### compareTo의 일반 규약
1. 반사성 : x.compareTo(x) == 0
2. 대칭성 : x.compareTo(y) > 0 이면 y.compareTo(x) < 0이다.
3. 추이성 : x.compareTo(y) > 0 && y.compareTo(z) > 0이면 x.compareTo(z) > 0이다.
4. 일관성 : x.compareTo(y) == 0 && y.compareTo(z) == 0이면 x.compareTo(z) == 0이다.
* compareTo를 활용한 동치성 검사는 equals와 같아야 한다. (필수는 아니지만 권장) 
> Tree구조에서는 비교를 compareTo로 하기 때문에 BigDecimal("1.0")과 BigDecimal("1.00")은 같다. (equals에서는 다름)

### Comparable을 구현한 클래스의 상속
상속받은 클래스에서 Comparable을 구현하거나 오버라이드하는 것은 불가능 <br>
-> Composition(조합)을 사용해서 구현
```
public class NamedPoint implements Comparable<NamedPoint> {

    private final Point point;
    private final String name;

    public NamedPoint(Point point, String name) {
        this.point = point;
        this.name = name;
    }

    @Override
    public int compareTo(NamedPoint namedPoint) {
        int result = this.point.compareTo(namedPoint.point);
        if (result == 0) {
            result = this.name.compareTo(namedPoint.name);
        }
        return result;
    }
}
```
* Point 비교 후 name을 비교해서 정렬

### Comparator
* 자바 8부터는 Compartor 인터페이스를 활용해서 비교자 생성 메서드와 메서드 연쇄 방식으로 비교자를 생성할 수 있게 되었다.
```
Comparator.comparingInt((Point p) -> p.getX())  // x를 기준으로 오름차순
                .thenComparing(p -> p.getY());  // y를 기준으로 오름차순
```
장점: 코드를 읽기가 편하다.<br>
단점: 약간의 성능 저하가 뒤따른다.
