# 32장 제너릭과 가변인수를 함께 쓸 때는 신중하라
#### 제너릭과 가변인수를 혼용한 예
```
static void dangerous(List<String>... stringLists) {    //가변인수
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;     // 얕은 복사
    objects[0] = intList;               // 힙 오염 발생
    String s = stringLists[0].get(0);   // ClassCastException
}
```
* 위의 코드처럼 제너릭과 가변인수를 같이 쓰게 된다면 타입 안정성을 해칠 수 있다.

#### 제너릭과 가변인수(varargs)가 허용되는 예
```
Arrays.asList(T... a);
Collections.addAll(Collection<? super T> c, T... elements);
EnumSet.of(E first, E... rest);
```
* 위의 예시처럼 제너릭이나 매개변수화 타입의 varargs 매개변수를 받는 메서드는 실무에서 매우 유용하기 때문에 허용된다.

#### 제너릭 가변인자를 안전하게 사용하는 2가지 경우
1. 제너릭 가변인자에 아무것도 넣지 않는다.
```
@SafeVarargs
static <T> List<T> flatten(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists)
        result.addAll(list);
    return result;
}
```
2. 제너릭 가변인자를 컴파일러가 내부적으로 만든 제너릭 배열을 밖에 노출하지 않는다.
```
// 배열 대신 List를 이용해 안전하게 바꿘 PickTwo (196쪽)
static <T> List<T> pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return List.of(a, b);
        case 1: return List.of(a, c);
        case 2: return List.of(b, c);
    }
    throw new AssertionError();
}
```
* 안전하게 사용하기 위해 가변인자 대신 List.of를 사용한다.
* 가변 인자 대신 List를 사용하면 실수로 안전하지 않은 코드에 @SafeVarargs를 붙이는 일도 없어지므로 코드가 안전해진다.

#### @SafeVarargs
* 1번처럼 안전하게 사용하는 경우 제너릭이나 매개변수화 타입의 가변인자(varargs) 매개변수를 받는 모든 메서드에 @SafeVarargs를 붙인다.

> 가변인자와 제너릭은 같이 사용하면 타입이 안전하지 않지만 허용된다. 메서드의 타입이 안전한지 확인한 다음 @SafeVarargs 애너테이션을 달아 사용하는 데 불편함이 없게끔 하자. 