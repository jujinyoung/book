# 11장 equals를 재정의하려거든 hashCode도 재정의하라

```
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");

System.out.println(m.get(new PhoneNumber(707, 867, 5309))); //null
```

> 해시코드를 구현하지 않는다면 <b>null</b>로 판단

### hashCode의 일반 규약
1. equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode는 변하면 안된다.
2. equals가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.
3. equals가 두 객체를 다르다고 판단했어도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. (다른 객체는 다른 값을 반환해야 성능이 좋아짐)

> 두 객체가 다르더라도 hashCode가 같다면 같은 해시테이블의 버킷에 담겨 연결 리스트처럼 동작한다. 
> 
> 속도: 해시 자료구조 O(1) -> 해시충돌 시 연결리스트 O(n)
> 
> * 자바 8이후에는 버킷에 일정 개수 이상의 엔트리가 추가되면 연결 리스트(O(n)) 대신 이진 트리(Olog(n))를 사용하도록 변경됨.

### hashCode 구현
```
@Override public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    retun result;
}
```
* Objects.hash를 사용해서 구현해도 같음.
* 만약 필드가 final이고 해시코드를 계산하는 비용이 크다면 인스턴스가 만들어질때 해시코드를 계산 후 캐싱한다. (멀티쓰레드 환경에서는 조심)
#### 31을 써야 하는 이유
1. 홀수를 써야 한다.
2. 31이라는 소수를 썻을때 해시 충돌이 제일 적게 난다.

### HashCode 구현 시 주의사항
1. 성능을 위해 해시코드를 계산할 때 핵심 필드를 생략해서는 안 된다.
2. 해시코드의 로직을 API 사용자에게 자세히 공표해서는 안된다. (클라이언트가 해당 값에 의존할수도 있음 -> 추후 계산 방식 변경시 문제)

