# 18장 상속보다는 컴포지션을 사용하라
* 구현 상속은 코드를 재사용하는 강력한 수단이지만 무분별한 사용은 지양해야 한다.
* 상위 클래스와 하위 클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 구현 상속도 안전한 방법이다.

#### 상속을 잘못 구현한 예제
```
public class InstrumentedHashSet<E> extends HashSet<E> {

    //추가된 원소의 수
    private int addCount = 0;

    public InstrumentedHashSet(){}

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c); //문제 발생
    }

    public int getAddCount() {
        return addCount;
    }

    public static void main(String[] args) {
        InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
        s.addAll(List.of("틱", "탁탁", "팡"));
        System.out.println(s.getAddCount());    //예상 결과 3, 실제 결과 6
    }
}
```
* addAll은 add를 내부 호출하는 문제 -> 상속받는 부모의 addAll의 내부 호출방식을 알아야 한다. -> 캡슐화가 깨지는 문제
* addCount += c.size();를 주석처리 하는 방법 -> 상위 클래스의 addAll() 메서드가 변경된다면 문제 발생

#### Composition 사용
```
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) {this.s = s;}
    public int size() {return s.size();}
    public boolean isEmpty() {return s.isEmpty();}
    public boolean contains(Object o) {return s.contains(o);}
    public Iterator<E> iterator() {return s.iterator();}
    public Object[] toArray() {return s.toArray();}
    public <T> T[] toArray(T[] a) {return s.toArray(a);}
    public boolean add(E e) {return s.add(e);}
    public boolean remove(Object o) {return s.remove(o);}
    public boolean containsAll(Collection<?> c) {return s.containsAll(c);}
    public boolean addAll(Collection<? extends E> c) {return s.addAll(c);}
    public boolean retainAll(Collection<?> c) {return s.retainAll(c);}
    public boolean removeAll(Collection<?> c) {return s.removeAll(c);}
    public void clear() {s.clear();}
}
```
* Set을 멤버변수로 선언 후 기능을 위임하는 ForwardingSet 클래스를 만든 후 해당 클래스를 상속받게 한다.
* 해당 방식으로 구현 후 사용할 경우 HashSet의 기능이 추가되거나 변경이 일어나도 InstrumentedHashSet에서는 영향을 받지 않는다.