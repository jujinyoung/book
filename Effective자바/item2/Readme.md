# 2장 생성자에 매개변수가 많다면 빌더를 고려하라
### 정적 팩터리와 생성자에 매개변수가 많을 때 고려할 수 있는 방안
#### 1. 점층적 생성자 패턴
```
public class NutritionFacts {

    private int servingSize;
    private int servings;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }
    
    ... 매개변수 1개씩 줄면서 반복
}
```
> **단점1**: 매개변수가 늘어나면 클라이언트 코드를 작성하거나 읽기 어렵다.<br>
> **단점2**: 클라이언트의 실수로 매개변수의 순서를 바꿔도 컴파일러는 알아채지 못하고 엉뚱한 동작을 하게 된다.

#### 2. 자바빈즈 패턴
```
public class NutritionFactsJavaBeans {

    private int servingSize;
    private int servings;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;

    public void setServingSize(int servingSize) { this.servingSize = servingSize; }
    public void setServings(int servings) { this.servings = servings; }
    public void setCalories(int calories) { this.calories = calories; }
    public void setFat(int fat) { this.fat = fat; }
    public void setSodium(int sodium) { this.sodium = sodium; }
    public void setCarbohydrate(int carbohydrate) { this.carbohydrate = carbohydrate; }
}
```
> **단점1**: set을 통해 객체를 완성하기 전에는 일관성(consistency)가 무너진 상태에 놓이게 된다.<br>
> **단점2**: 일관성이 무너지는 문제때문에 클래스를 불변으로 만들 수 없다.
* 이러한 단점을 완화하고자 생성이 끝난 객체를 얼리고 사용할 수 없게 하는 방법도 존재한다. (실무 사용 X)
#### 빌더 패턴
```
public class NutritionFacts {

    private int servingSize;
    private int servings;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;

    public static class Builder {
        //필수 매개변수
        private final int servingSize;
        private final int servings;

        //선택 매개변수 - 기본값으로 초기화한다.
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```
#### 사용
```
NutritionFacts nutrition = new NutritionFacts
                .Builder(10, 100)
                .calories(20)
                .fat(30)
                .sodium(10)
                .carbohydrate(40)
                .build();
```
### 롬복을 활용한 빌더 패턴
```
@Builder
@AllArgsConstructor(access = AccessLevel.PRIVATE) //주의사항1
public class NutritionFacts {

    private int servingSize;
    private int servings;
    private int calories;
    private int fat;
    private int sodium;
    private int carbohydrate;
}
```
#### 사용
```
NutritionFacts lombok = NutritionFacts
                .builder()
                .servings(10)
                .build();
```
> 주의사항<br>
> 1.모든 파라미터를 가진 기본 생성자도 열리는 문제 =>  access레벨을 private으로 해결<br>
> 2.필수 필드를 지정할 수 없는 문제

### 계층형 빌더
```
public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            //self() 메서드로 자식 클래스의 this를 받아옴 -> Builder<T>를 리턴하면 클라이언트에서 사용 시 캐스팅 필요
            return self();
        }

        abstract Pizza build();

        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
    }
}
```
```
public class NyPizza extends Pizza{
    public enum Size{ SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;
        
        private boolean sauceInside = false;    //기본값

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}
```
#### 실제 사용
```
NyPizza nyPizza = new NyPizza.Builder(SMALL)
                .addTopping(PEPPER)
                .addTopping(MUSHROOM)
                .sauceInside()  //바로 사용
                .build();
```
* Pizza 클래스에서 return을 NyPizza의 self() 로 넘겨줬기 때문에 typeCasting이 불필요
* 그렇기 때문에 sauceInside를 캐스팅 없이 바로 체이닝방식으로 사용가능








