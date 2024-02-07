# 5장 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
### 자원을 직접 명시하지 마라
```
private static final Dictionary dictionary = new Dictionary();
```
* 사전의 종류는 언어별로(리소스에 따라) 쓰임새가 다른데 위의 코드 처럼 직접 명시해버리면 확장이나 교체 등이 어렵다.
* 사용하는 자원에 따라 동작이 달라지는 클래스는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

### 의존 객체 주입을 사용하라
```
private final Dictionary dictionary;

public SpellChecker(Dictionary dictionary) {
    this.dictionary = dictionary;
}
```
* 의존 객체를 주입하면 Dictionary가 인터페이스일 경우 클래스 내부의 모든 메소드를 재사용 할 수 있다.

### 생성자에 자원 팩터리를 넘겨주는 방식
```
private final Dictionary dictionary;

public SpellCheck(Supplier<? extends Dictionary> dictionaryFactory) {
    this.dictionary = dictionaryFactory.get();
}
```
* Dictionary 하위 타입의 모든 Supplier를 인자로 주면 Supplier는 생성되는 타입을 주입
* 굳이 Supplier를 사용할 필요가 있나 싶음, 의존 객체 주입방식을 사용해도 알아서 구현객체 주입