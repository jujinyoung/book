# 25장 톱 레벨 클래스는 한 파일에 하나만 담으라
```
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
```
* 컴파일 오류 발생

#### 해결책
```
public class Test {
    private static class Utensil {
        static final String NAME = "pot";
    }
    
    private static class Dessert {
        static final String NAME = "pie";
    }
}
```
* 정적 멤버 클래스로 만들어서 사용
