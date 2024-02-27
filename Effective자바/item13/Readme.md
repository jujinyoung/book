# 13장 clone 재정의는 주의해서 진행하라
### clone 메서드의 일반 규약
1. x.clone() != x,  true
2. x.clone().getClass() == x.getClass(), true
3. x.clone().equals(x), true가 아닐 수도 있다.

### clone 재정의시 주의사항
1. 상속용 클래스에서는 Cloneable을 구현하면 안된다.
2. Object의 clone메서드는 동기화를 신경쓰지 않았다. (동시성 문제 발생)
3. 재정의한 clone메서드는 throws절을 없애야한다. (클라이언트 사용 편의성)

### super.clone() vs 생성자 호출
```
public class Item implements Cloneable {

    private String name;

    ///생성자를 통한 clone구현
    @Override
    public Item clone() {
        Item item = new Item();
        item.name = this.name;
        return item;
    }

    ///super.clone을 활용한 구현
    @Override
    protected Item clone() {
        Item result = null;
        try {
            result = (Item) super.clone();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```
* 생성자 호출을 통해 구현한 clone의 경우 Item을 구현한 하위클래스에서 clone 호출 시 캐스팅 에러가 발생한다. 
> Cloneable 인터페이스를 구현할 경우 반드시 super.clone을 통해 clone()을 구현하도록 하자

### 가변객체의 clone
```
private Object[] elements;

//shallow copy(얕은 복사)
@Override
public Stack clone() {
    try {
        Stack result = (Stack) super.clone();
        result.elements = elements.clone();
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    }
}
```
* 가변객체(elements)의 경우 clone을 통해 복사를 해도 같은 메모리를 참조함.
```
public class HashTable implements Cloneable {

    private Entry[] buckets = new Entry[10];

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        public Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }

        //엔트리 자신이 가르키는 연결 리스트를 반복적으로 복사한다.
        Entry deepCopy() {
            Entry result = new Entry(key, value, next);
            for (Entry p = result; p.next != null; p = p.next) {
                p.next = new Entry(p.next.key, p.next.value, p.next.next);
            }
            return result;
        }
    }

    @Override
    public HashTable clone() {
        try {
            HashTable result = (HashTable) super.clone();
            result.buckets = new Entry[buckets.length];
            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null) {
                    result.buckets[i] = buckets[i].deepCopy();
                }
            }
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }

    public static void main(String[] args) {
        HashTable hashTable = new HashTable();
        Entry entry = new Entry(new Object(), new Object(), null);
        hashTable.buckets[0] = entry;
        HashTable clone = hashTable.clone();
        System.out.println(hashTable.buckets[0] == entry);              //true
        System.out.println(hashTable.buckets[0] == clone.buckets[0]);   //false
    }
}
```
* deepCopy() 메서드에서 값만 복사해서 clone을 구현(깊은 복사)

### 복사 생성자와 복사 팩터리
#### 복사 생성자 : 자신과 같은 클래스의 인스턴스를 인수로 받는 생성자
```
public Yum(Yum yum) {...};
```
* 생성자를 통해서 만들어지기 때문에 명확함 

#### 복사 팩터리
```
public static Yum newInstance(Yum yum) {...};
```
> 복사 생성자와 복사 팩터리는 Cloneable/clone 방식과 달리 final 필드 용법과도 충돌하지 않으며, 불필요한 검사 예외를 던지지도 않고 형변환도 필요치 않다.

### 정리
> * 새로운 인터페이스를 만들 때는 절대 Cloneable을 확장해서는 안되며, 새로운 클래스도 이를 구현해서는 안된다.
> * final 클래스라면 성능 최적화 관점에서 검토후 문제가 없을떄만 Cloneable을 구현해도 된다.
> * <b>복제 기능은 생성자와 팩터리를 이용하는 것이 최고</b>