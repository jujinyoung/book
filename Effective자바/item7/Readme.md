# 7장 다 쓴 객체 참조를 해제하라
#### 간단히 스택을 구현한 코드
```
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack(Object[] e) {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    //문제 발생
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```
* push를 반복하고 pop을 해도 꺼내진 객체들을 GC가 회수하지 못한다. -> 심각할 경우 OutOfMemoryError 발생
* pop시 다 쓴 객체를 null처리 하면 GC의 대상이 된다. -> 추후 NullPointException 발생 가능성
```
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    Object result = elements[--size];
    element[size] = null;   // 다 쓴 참조 해제
    return result;
}
```
> 메모리를 직접 관리하는 클래스라면 메모리 누수에 주의해야 한다. 


### 캐시
객체 참조를 캐시에 넣고 나서, 놔두면 메모리 누수 <br>
???? 나머지 이해안됨















