# 6장 불필요한 객체 생성을 피하라
### String 객체
```
//사용금지
String s = new String("bikini");

//사용
String s = "bikini";
```
* 문자열은 자바의 String pool에서 관리하기때문에 아래처럼 사용하면 재사용이 가능하다.

### 불필요한 오토박싱을 피할 것
```
private static long sum() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i;
    }
    
    return sum;
}
```
* sum은 래퍼클래스로 되어 있고 i는 원시타입으로 되어 있어서 불필요한 Long인스턴스가 2<sup>31</sup>개나 만들어짐 -> 박싱된 기본 타입보다는 기본 타입을 사용할 것
