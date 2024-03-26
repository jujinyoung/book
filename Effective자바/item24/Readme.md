# 24장 멤버 클래스는 되도록 static으로 만들라
중첩 클래스의 종류
1. 정적 멤버 클래스
2. (비정적) 멤버 클래스
3. 익명 클래스
4. 지역 클래스

정적 멤버 클래스를 제외한 나머지는 내부 클래스에 해당한다.

#### 정적 멤버 클래스
```
public class OutterClass {
    private static int number = 10;
    
    private static class InnerClass {
        void doSomething() {
            System.out.println(number);
        }
    }
}
```
> InnerClass innerClass = new InnerClass();

#### 비정적 멤버 클래스
```
public class OutterClass {
    private int number = 10;
    
    private class InnerClass {
        void doSomething() {
            System.out.println(number);
        }
    }
}
```
> InnerClass innerClass = new OutterClass().new InnerClass();

* 비정적 멤버 클래스와 정적 멤버 클래스의 구문상 차이는 단지 static여부이다.
* 비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다.

> 멤버 클래스에서 바깥 인스턴스를 참조할 필요가 없다면 무조건 정적 멤버 클래스로 만들자.