# 27장 비검사 경고를 제거하라
### 비검사 경고
* 컴파일러가 타입 안정성을 확인하는데 필요한 정보가 충분치 않을 때 발생시키는 경고.
```
Set<Lark> exaltation = new HashSet();   -> [unchecked] unchecked conversion 경고 표시
Set<Lark> exaltation = new HashSet<>(); -> 해결
```
* 할 수 있는 한 모든 비검가 경고를 제거해야한다.

### @SuppressWarnings
* 경고를 제거할 수는 없지만 타입이 안전하다고 확인한다면 @SuppressWarnings("unchecked")를 사용한다.
* 해당 어노테이션은 가능한 가장 좁은 범위에 적용해야한다.
```
//@SuppressWarnings("unchecked") -> 메서드에 붙일경우 다른부분의 경고를 무시해버릴수도 있음
public <T> T[] toArray(T[] a) {
    if(a.length < size) {
        /** 경고를 무시해도 안전한 이유를 주석으로 작성 */
        @SuppressWarnings("unchecked") 
        T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
        return result;  
    }
    ...
}
```