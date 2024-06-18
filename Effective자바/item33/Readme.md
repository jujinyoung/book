# 33장 타입 안전 이종 컨테이너를 고려하라
* 일반적인 제너릭 형태에서는 한 컨테이너가 다룰 수 있는 타입 매개변수의 수가 고정되어 있다. => 타입 안전 이종 컨테이너를 사용 
#### 타입 안전 이종 컨테이너
* 컨테이너 대신 **키를 매개변수화**한 다음, 컨테이너에 값을 넣거나 뺄 때 **매개변수화한 키를 함께 제공**하면 제너릭 타입 시스템이 값의 타입이 키와 같음을 보장한다.
```
//API 코드
public class Favorites {
    private Map<Class<?>, Object> favoirtes = new HashMap<>();
    
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }
    
    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}

//클라이언트 코드
public static void main(String[] args) {
    Favorites f = new Favorites();
    
    f.putFavorite(String.class, "Java");
    f.putFavorite(Integer.class, 123456);
    
    String favoriteString = f.getFavorite(String.class);
    int favoriteInteger = f.getFavorite(Integer.class);
}
```
* Favorites의 인스턴스 타입은 안전하고 여러 종류를 담을 수 있으므로 타입 안전 이종 컨테이너이다.
* favorites의 타입은 Map<Class<?>, Object>이므로 비한정적 와일드 카드이다.
* 그래서 이 맵안에 아무것도 넣을 수 없다고 생각되지만 와일드 카드 타입이 중첩되었으므로 **모든 키가 서로 다른 매개변수화 타입**일 수도 있다.

#### 타입 토큰
* 컴파일타임 타입정보와 런타임 타입 정보를 알아내기 위해 메서드들이 주고받는 class 리터럴을 타입토큰(Type Token)이라고 한다.
> 추후 재정리 모르겠음 뭐라고 하는지