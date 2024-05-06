# 3장 this
#### this의 개념
* this는 자바스크립트에서 매우 중요한 키워드로, 함수가 호출될 때마다 동적으로 결정된다.
* this는 실행 컨텍스트가 생성될 때 thisBinding에 저장되며, 함수가 호출되는 방식에 따라 달라진다.

### 상황에 따라 달라지는 this
#### 전역공간에서의 this
* 브라우저 환경 = window
* node.js 환경 = global
```
var a = 1;
console.log(a);         //1
console.log(window.a);  //1
console.log(this.a);    //1
```
* **자바스크립트이 모든 변수는 특정 객체(LexicalEnvironment)의 프로퍼티**로서 동작
* 그렇기 때문에 window.a, this.a는 1이 나온다.
* a를 직접 호출할때도 1이 나오는 이유는 스코프 체인에서 a를 검색하다 가장 마지막에 전역 스코프의 전역객체의 a프로퍼티를 발견해서 반환해준다.
* const, let을 전역 변수로 선언하면 전역 객체에 저장되는 것이 아닌 보이지 않는 개념적인 블록에 저장되기 때문에 window.a, this.a로는 출력 불가능

#### 메서드로서 호출할 때 그 메서드 내부의 this
* 메서드로서 내부의 this에는 호출에 대한 정보가 담긴다.
```
var obj = {
    methodA: function() { console.log(this); },
    inner: {
        methodB: function() { console.log(this); }
    }
};
obj.methodA();          //obj
obj['methodA']();       //obj

obj.inner.methodB();        //obj.inner
obj.inner['methodB']();     //obj.inner
obj['inner'].methodB();     //obj.inner
obj['inner']['methodB']();  //obj.inner
```

#### 함수로서 호출할 때 그 함수 내부에서의 this
* 함수를 함수로서 호출할 경우에는 this가 지정되지 않는다. (this에는 호출한 주체에 대한 정보가 담겨야함) 
* 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 코드에 직접 관여해서 실행하기 때문에 호출 주체의 정보를 알 수 없다.
* 따라서 함수에서의 this는 전역 객체를 바라본다.
```
var obj1 = {
    outer: function() {
        console.log(this);          //(1)
        var innerFunc = function() {
            console.log(this);      //(2), (3)
        }
        innerFunc();
        
        var obj2 = {
            innerMethod: innerFunc
        };
        obj2.innerMethod();
    }
};
obj1.outer();
```
* (2): innerFunc를 호출한 결과
* (3): obj2.innerMethod를 호출한 결과
* 결과
  * (1): obj1
  * (2): 전역객체(Window) -> 함수명 앞에 .이 없으므로 함수호출, this가 지정되지 않았기 때문에 스코프 체인상 최상위 객체인 전역객체(window)가 바인딩 된다.
  * (3): obj2

#### 화살표 함수
* ES6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자, this를 바인딩하지 않는 화살표 함수를 도입
```
var obj = {
    outer: function() {
        console.log(this);  //outer
        var innerFunc = () => {
            console.log(this);  //outer
        };
        innerFunc();
    }
};
obj.outer();
```
* 함수로서 호출할때 호출 주체가 없더라도 자동으로 전역 객체를 바인딩 하지 않고 **호출 당시 주변 환경의 this를 그대로 상속**받아서 사용

#### 콜백 함수 호출 시 그 함수 내부에서의 this
4장에서 자세히 다룰 예정

#### 생성자 함수 내부에서의 this
생성자에서의 this는 생성될 객체 자체를 가르킨다.
```
var Cat = function (name, age) {
    this.bark = '야옹';
    this.name = name;
    this.age = age;
};
var choco = new Cat('초코', 7);
var nabi = new Cat('나비', 5);
console.log(choco, nabi);
```
* 결과
  * Cat { bark: '야옹', name: '초코', age: 7 }
  * Cat { bark: '야옹', name: '나비', age: 5 }

### 명시적으로 this를 바인딩하는 방법
#### call(), apply()
* call 메서드는 호출 주체인 함수를 즉시 실행하도록 명령으로, **첫 번째 인자를 this로 바인딩**하고 이후의 인자들을 호출할 함수의 매개변수로 전달한다.
* apply 메서드는 call과 동일한 역할을 수행하지만 두 번째 인자를 배열로 받는다.
* 함수를 그냥 실행하면 this는 전역객체를 참조하지만 call이나 apply를 사용하면 **임의의 객체를 this로 지정**할 수 있다.
```
var func = function(a, b, c) {
  console.log(this, a, b, c);
}

func(1, 2, 3);  // window 1 2 3
func.call({x: 1}, 4, 5, 6); // {x: 1} 4 5 6
func.apply({x: 2}, [4, 5, 6]);  // {x: 2} 4 5 6
```

#### bind()
* bind 메서드는 call과 비슷하지만 즉시 호출이 아닌 새로운 함수를 반환한다.
* 함수에 this를 미리 적용하거나 부분 적용 함수를 구현 시 사용
* bind를 통해 새로 만든 함수는 name 프로퍼티에 bound라는 접두어가 붙어서 코드 추적이 쉽다.

```
var func = function(a, b, c, d) {
  console.log(this, a, b, c, d);
};

func(1, 2, 3, 4); // window 1 2 3 4

//this 적용
var bindFuc1 = func.bind({x: 1});
bindFuc1(5, 6, 7, 8); // {x: 1} 5 6 7 8

//부분 함수 적용
var bindFunc2 = func.bind({x: 1}, 4, 5);
bindFunc2(6, 7);  // {x: 1} 4 5 6 7
bindFunc2(8, 9);  // {x: 1} 4 5 8 9
```