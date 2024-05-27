# 29장 이왕이면 제너릭 타입으로 만들라
> 클라이언트에서 직접 형변환해야 하는 타입보다 제너릭 타입이 더 안전하고 쓰기 편하다. 
> 새로운 타입을 설계할때는 형 변환 없이 사용할 수 있게 하자.
 
### 스택을 Generic으로 만드는 방법1
```
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    @SuppressWarnings("unchecked")  //최초 생성시 한번 형변환할경우 pop에서는 형변환할 필요가 없음
    public Stack() {
        this.elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY]; //new E(제너릭 배열)은 허용X
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        E result = elements[--size];
        elements[size] = null;  //다 쓴 참조 해제
        return result;
    }
    ...
}
```

### 스택을 Generic으로 만드는 방법2
```
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {     // push의 타입은 E다
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        
        // push에서 E 타입만 허용하므로 이 형변환은 안전하다.
        @SuppressWarnings("unchecked") E result = (E) elements[--size];
        
        elements[size] = null;  //다 쓴 참조 해제
        return result;
    }
    ...
}
```
* 첫번째 방법은 스택 생성시에 형변환을 한번만 하지만 두번째 방법은 배열에서 원소를 읽을때마다 해줘서 첫번째 방법이 더 선호된다.
* 하지만 배열의 런타임 타입이 컴파일타임 타입과 달라 **힙 오염**이 발생한다. (아이템32)
* Stack의 예는 아이템 28(배열보다는 리스트를 사용하라)와는 모순돼지만 제너릭 타입 안에서는 리스트를 사용하는 게 항상 가능하지가 않다. 특히 자료구조를 구현한 클래스의 경우 성능으로 인해 배열을 선호한다.  