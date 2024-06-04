# 30장 이왕이면 제너릭 메서드로 만들라
### 제너릭 메서드
```
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```
* 제너릭 타입 작성법과 비슷하다.
* 타입 매개변수 목록은 메서드의 제한자와 반환타입 사이에 위치
* ex) public 과 Set 사이

### 제너릭 싱글턴 팩토리
* 불변 객체를 여러 타입으로 활용해야 할 때가 있는데 제너릭은 런타임시 타입 정보가 소거 되므로 하나의 객체를 어떤 타입으로든 **매개변수화** 할 수 있다.
```
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

// 요청한 타입 매개변수에 맞게 매번 바꿔주는 정적 팩토리
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

### 재귀적 타입 한정
```
public static <E extends Comparable<E>> E max(Collection<E> c);
```
* E로 받을 타입은 오직 Comparable<E>를 구현한 타입만 가능하다.

```
//재귀적 타입 한정을 이용한 최댓값 계산 메서드
public static <E extends Comparable<E>> E max(Collection<E> c){
    if(c.isEmpty()){
       throw new IllegalArgumentException("컬렉션이 비었습니다.");
    }
        
    E result = null;
    for (E e : c){
        if(result == null || e.compareTo(result) > 0){
            result = Objects.requireNonNull(e);
        }
    }
        
    return result;
}
```
* 컴파일 오류나 경고는 발생하지 않으며 컬렉션에 담긴 원소의 자연적 순서를 기준으로 최댓값을 계산한다.
