# 28장 배열보다는 리스트를 사용하라
### 배열과 제너릭의 차이 두가지
#### 1. 배열(공변) vs 제너릭(불공변)
* 공변: 같이 변한다. (상속관계에 따라)
* 불공변: 같이 변하지 않는다.
```
//배열 - 문법상 허용(런타임 실패)
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."  //ArrayStoreException

//매개변수화 타입 - 문법상 불허용(컴파일 실패)
List<Object> ol = new ArrayList<Long>();
ol.add("타입이 달라 넣을 수 없다.);
```

#### 2. 배열은 실체화된다.
* 배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하지만(**ArrayStoreException**) 제너릭은 타입 정보가 런타임에는 소거된다.
* 즉, 제너릭은 컴파일시에만 원소 타입을 검사하며 런타임에는 알 수 없다.
```
List<String>[] stringLists = new List<String>[1];   //제너릭 배열이 허용된다고 가정
List<Integer> intList = List.of(42);
Object[] objects = stringLists;
objects[0] = intList;               
String s = stringLists[0].get(0);   //배열에서 요소를 꺼내려고할때 ClassCastException 발생
```
> 제너릭의 취지는 컴파일 시점에 ClassCastException이 발생하는 것을 방지하기 위해서인데 제너릭 배열을 허용하면 **런타임 시점에 ClassCastException이 발생**한다.

### 배열대신 컬렉션을 사용

#### 제너릭을 적용하기 이전
```
public class Chooser {
    private final Object[] choiceArray;

    public Chooser(Object[] choiceArray) {
        this.choiceArray = choiceArray;
    }

    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

#### 제너릭을 적용
```
public class GenericChooser<T> {
    private final List<T> choiceArray;

    public GenericChooser(Collection<T> choiceArray) {
        this.choiceArray = new ArrayList<>(choiceArray);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray.get(rnd.nextInt(choiceArray.size()));
    }
}
```
* 배열대신 리스트를 사용해서 런타임 시점에 ClassCastException을 방지한다. -> 배열을 리스트로 대체