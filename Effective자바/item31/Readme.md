# 31장 한정적 와일드카드를 사용해 API 유연성을 높이라
### 불공변인 매개변수화 타입
* 매개변수화 타입(제너릭)은 불공변이기 때문에 List<String.>은 List<Object.>의 하위 타입이 아니다.
* 하위 상위 타입을 포용하지 않게 되어있다. (아이템 28번)
* 하지만 때론 불공변 방식보다 유연한 무언가가 필요할 때 **한정적 와일드카드타입**을 사용한다.

#### 생산자(producer)매개변수에 와일드카드 타입 적용
```
public void pushAll(Iterable<? extends E> src) {
    for (E e : src) push(e);
}
```

#### 소비자(consumer)매개변수에 와일드카드 타입 적용
```
public void popAll(Collection<? super E> dst) {
    while(!isEmpty()) dst.add(pop());
}
```

### 펙스(PECS)
```
Producer - Extends
Consumer - Super
```
* 상위 타입일수록 더 적은 정보를 가지고 있다. 하위 타입일수록 더 구체적인 정보를 가지고 있다.
* 생산자일 때는 하위 타입을 받아도 객체에 필요한 정보만 쓰면 되니, extends를 쓴다.
* 소비자일 때는 소비자의 정보를 받아줄 타입이 필요 하니, super를 쓴다.

#### PECS 공식 사용 전
```
public static <E> Set<E> union(Set<E> s1, Set<E> s2)
```

#### PECS 공식 사용 후
```
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
```
* 반환타입에는 한정적 와일드 카드 타입을 사용하면 안된다. (그렇게 된다면 클라이언트 코드에서도 와일드카드 타입을 사용)

#### 클라이언트 코드
```
Set<Integer> integers = Set.of(1, 3, 5);
Set<Double> doubles = Set.of(2.0, 4.0, 6.0);
Set<Number> numbers = union(integers, doubles);
```

> 뒤쪽 이해 불가 - 언젠가 다시 보기