# 17장 변경 가능성을 최소화하라 

#### 불변 클래스 = 인스턴스 내부의 값을 수정할 수 없는 클래스

### 불변 클래스의 다섯 가지 규칙
1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다. (setter 제공 X)
2. 클래스를 확장할 수 없도록 한다. (class에 final 붙이기)
3. 모든 필드를 final로 선언한다. (새로 생성된 인스턴스를 동기화 없이 다른 스레드로 건네도 문제없이 동작 보장)
    > final은 쓸 수 있는 한 최대한 쓰면 좋다. (성능 이점, 코드를 더 견고하게 해줌)
4. 모든 필드를 private으로 선언한다. (클라이언트 필드에 직접 접근해 수정하는 일 방지)
5. 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다. (방어적인 복사를 사용할 것)

### 불변 클래스의 장점
1. <b>스레드 안전</b>
2. <b>실패 원자성</b>
   * 작업 수행 중 예외가 발생해도 객체가 일관되게 변경되지 않는다.
3. <b>시간적 결합 감소</b>
   * 객체의 상태가 변경될 수 없으므로, 객체 사용 시점과 객체가 생성된 시점 사이의 상태 변화에 대해 걱정할 필요가 없다.
4. <b>메모리상의 효율성</b>
   * 불변 객체는 자유롭게 공유가 가능하기 때문에, 같은 값을 가지는 객체를 중복해서 생성할 필요가 없다.
5. <b>보안성</b>
   * 불변 객체는 외부에서 내부 상태를 변경할 수 없으므로, 보안상의 위험에서 보다 안전하다.
6. <b>캐싱과 재사용 용이</b>
   * 불변객체는 값이 변하지 않기 때문에 필요할 때마다 재사용할 수 있다. 
7. <b>디버깅 용이</b>
   * 불변 객체는 그 값이 변하지 않기 떄문에 디버깅 시 객체 상태를 추적하기가 쉽다.

### 불변 클래스의 단점
1. 메모리 사용량과 성능 문제
   * <b>값이 다르면 반드시 독립된 객체로 만들어야 한다.</b> 이는 빈번한 객체 생성으로 이어지고, 결과적으로 GC에 많은 부하를 줄 수 있다.
2. 구현의 복잡성
   * 불변 객체를 설계할 때는 객체의 모든 가능한 상태를 생성자나 정적 팩토리 메소드를 통해 제공해야하기 때문에 상태 변화가 복잡한 객체의 경우, 모든 기능을 제공하는게 어려울 수 있다.
3. 사용의 제약, 성능 최적화의 제한
   * 상태 변경이 필요한 경우 기존 객체를 변경하는 대신 새로운 객체를 생성해야한다.
4. 성능 최적화의 제한
   * 특정 상황에서 내부 상태를 직접 변경하는 것이 불가능하므로 최적화가 제한된다.

### 불변 클래스를 만들 때 고려할 것
1. 객체의 크기와 복잡성
   * 가능한 작고 단순한 객체로 설계하는 것이 좋다.
2. 재사용성 고려
   * 불변 객체는 상태가 같다면 내용이 동일하므로 재사용 가능, 자주 사용되는 객체의 인스터스를 캐싱하고 필요할때 재사용 -> 성능과 메모리 사용 최적화 가능
3. 올바른 불변성의 확보
   * 클래스 내부의 모든 필드가 final이어야 한다, 필드가 참조 타입일 경우, 해당 필드가 가리키는 객체 역시 불변이여야 한다.
4. API 설계
   * 불변 객체를 사용할 떄는 객체의 상태 변화를 다루는 메서드가 새로운 객체를 반환하도록 설계해야 한다.
5. 동등성의 구현: 
   * 불변 객체는 동일한 상태를 가지면 논리적으로 동일한 것으로 간주될 수 있기 때문에 equals와 hashCode 메서드를 올바르게 구현해야한다.

### 정리
1. 클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다. (Getter가 있다고 해서 무조건 Setter를 만들지는 말자)
2. 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자.
3. 다른 합당한 이유가 없다면 모든 필드는 private final이어야 한다.
4. 생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.