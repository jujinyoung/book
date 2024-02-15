# 8장 finalizer와 cleaner 사용을 피하라
### finalizer와 cleaner
* 자바에서 제공하는 객체 소멸자
* 즉시 수행된다는 보장이 없다.(예측할 수 없다.)
* 실행되지 않을 수도 있다. (수행 여부도 보장하지 않는다.)
* 성능에 심각한 문제가 있다.
* finalizer는 보안문제도 존재한다.
* close()를 호출하지 않았을 때를 대비한 안전망 역할을 한다.

#### finalizer
<b>GC가 객체를 메모리 해제하려고 할때 그 객체가 finalize를 재정의한 경우 Finallization Queue에 들어간 후 Finalizer에 의해 정리</b>

자바9 버전 이후로 deprecated 됨.
```
public class FinalizeTest {

    public static void main(String[] args) {
        int num = 0;
        while (num < 10000){
            Finalize finalize = new Finalize(num++);
            finalize = null;   //참조 해제
            System.gc();       //GC 호출
        }
    }

    static class Finalize {
        private int num;

        public Finalize(int num) {
            this.num = num;
        }

        @Override
        protected void finalize() throws Throwable {
            System.out.println(num + "번 finalize 실행");
        }
    }
}
```
* Object 클래스에 정의된 finalize를 Override하면 해당 객체가 GC 대상이 될 때 finalizer 메소드가 호출된다.
* 비순차적이며 실행이 보장되지 않는다.
* 즉, 사용 X

#### cleaner
자바9에서는 finalizer의 대안으로 cleaner를 소개
```
public class CleanerTest implements AutoCloseable{

    public static void main(String[] args) {
        int num = 0;
        while (num < 1000){
            CleanerTest cleaner = new CleanerTest(num++);
            cleaner = null;
            System.gc();       //GC 호출
        }
    }

    private static final Cleaner cleaner = Cleaner.create();

    //CleanerTest의 인스턴스를 참조하면 run실행 제대로 수행이 안됨. (순환 참조 문제)
    private static class State implements Runnable {
        int num;

        State(int num) {
            this.num = num;
        }

        @Override
        public void run() {
            System.out.println(num);
            num = 0;
        }
    }

    // 방의 상태. cleanable과 공유한다.
    private final State state;

    private final Cleaner.Cleanable cleanable;

    public CleanerTest(int num) {
        state = new State(num);
        cleanable = cleaner.register(this, state);  //state라는 runnable을 실행
    }

    @Override
    public void close() {
        cleanable.clean();
    }
}
```
* cleaner도 마찬가지로 GC에 따라 실행여부가 불확실하다
* AutoCloseable을 구현
* close() 호출 시 cleaner가 실행되고 cleaner는 state스레드를 실행한다.
> run() 메서드의 호출 시점
> 1. close()를 직접 호출
> 2. GC의 대상이 될때까지 클라이언트가 close를 호출하지않아 cleaner가 직접 run()을 호출

* cleaner는 안전망 역할을 하지만 실행 여부가 불확실하기 때문에 중요한 자원 회수용으로는 사용을 <b>지양</b>한다.
* try-with-resources와 같이 사용하면 자원을 다 쓰고 나서 close()를 자동으로 호출해준다. (cleaner 안전망을 제대로 활용한 클라이언트 예시)