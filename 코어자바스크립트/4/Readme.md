# 4장 콜백 함수
### 콜백 함수란
* 다른 코드의 인자로 넘겨줌으로써 그 제어권도 함께 위임하는 함수이다.
* 콜백 함수의 this 또한 제어권을 위임받은 함수가 결정할 수 있다.

### 메서드를 콜백 함수로 전달
```
var obj = {
    vals: [1, 2, 3],
    logValues: function(v, i) {
        console.log(this, v, i);
    }
};
obj.logValues(1, 2);                // {vals:[1, 2, 3], logValues: f..}  1  2
[4, 5, 6].forEach(obj.logValues);   // Window { ... } 4 0
                                    // Window { ... } 5 1
                                    // Window { ... } 6 2
```
* obj.logValues(1, 2)는 객체.~~ 형태이므로 메서드로서 호출한 것이다. (this는 해당 객체)
* obj.logValues는 obj.logValues가 가르키는 함수만 전달 (logValues: f..), obj와의 연관성이 없어지므로 this는 전역객체를 바라보게 된다.
> 객체의 메서드를 콜백 함수로 전달하면 해당 객체를 this로 바라볼 수 없게 된다.

### 콜백 함수 내부에서 this가 객체를 바라보게 하는 방법
#### 전통적인 방식(ES5 이전): this를 다른 변수에 담아 콜백 함수를 사용할 함수에서는 this대신 그 변수를 사용하게 하고, 이를 클로저로 만든다.
```
var obj1 = {
    name: 'obj1',
    func: function() {
        var self = this;
        return function() {
            console.log(self.name);
        };
    }
};
var callback = obj1.func(); //obj1 출력
setTimeout(callback, 1000);
```
* 이 방식은 실제로 this를 사용하지 않고 번거로움
#### bind메서드 이용(ES5 이후)
```
var obj1 = {
    name: 'obj1',
    func: function() {
       console.log(this.name);
    }
};
setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = {name: 'obj2'};
setTimeout(obj1.func.bind(obj2), 1500);
```

### 콜백 지옥
#### 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상
```
setTimeout(function (name)) {
    var coffeeList = name;
    console.log(coffeeList);
    setTimeout(function (name)) {
        coffeeList += ', ' + name;
        console.log(coffeeList);
        setTimeout(function (name)) {
            coffeeList += ', ' + name;
            console.log(coffeeList);
        }, 500, '카페라떼');
    }, 500, '카페모카');
}, 500, '아메리카노');

//아메리카노
//아메리카노, 카페모카
//아케리카노, 카페모카, 카페라떼
```
* 비동기 제어를 위해 콜백 함수를 사용하다보면 콜백 지옥에 빠지기 쉽다.
* 최근에는 Promise, Generator, async/await 등 콜백 지옥에서 벗어나는 훌륭한 방법들이 등장
#### 해결책1 - 기명함수
```
var coffeList = '';

var addEspresso = function(name) {
    coffeeList = name;
    console.log(coffeeList);
    setTimeout(addAmericano, 500, '아메리카노');
};
var addAmericano = function(name) {
    coffeeList += ', ' + name;
    console.log(coffeeList);
    setTimeout(addAmericano, 500, '카페모카');
};
var addMocha = ...
setTimeout(addEspresso, 500, '에스프레소');
```
* 코드의 가독성을 높여주고 위에서부터 아래로 순서대로 읽는데 어려움이 없다.
* 하지만 일회성 함수를 전부 변수에 할당해야하는 문제

#### 해결책2 - Promise(ES6)
```
var addCoffee = function (name) {
    return function(prevName) {
        return new Promise(function(resolve) {
            setTimeout(function() {
                var newName = prevName ? (prevName + ', ' + name) : name;
                console.log(newName);
                resolve(newName);      
            }, 500);
        });       
    };
};
addCoffee('에스프레소')()
    .then(addCoffee('아메리카노'))
    .then(addCoffee('카페모카'))
    .then(addCoffee('카페라떼'));
```
* resolve 또는 reject 함수를 호출하는 구문이 있을 경우 실행 전까지 다음 구문으로 넘어가지 않는다.
* 비동기 작업의 동기적 표현이 가능

#### 해결책3 - Generator
```
var addCoffee = function(prevName, name) {
    setTimeout(function() {
        coffeeMaker.next(prevName ? prevName + ', ' + name : name);
    }, 500);
};
var coffeeGenerator = function* () {
    var espresso = yield addCoffee('', '에스프레소');
    console.log(espresso);
    var americano = yield addCoffee(espresso, '아메리카노');
    console.log(americano);
    var mocha = yield addCoffee(americano, '카페모카');
    console.log(mocha);   
};
var coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```
* function 뒤의 '*'이 Generator함수
* Generator는 Iterator를 반환하고 yield를 만나면 실행을 멈춘다. (순차적)

#### 해결책4 - Promise + Async/await(ES2017)
```
var addCoffee = function (name) {
    return new Promise(function(resolve) {
        setTimeout(function() {
            resolve(name);
        }, 500);
    });
};

var coffeeMaker = async function() {
    var coffeeList = '';
    var _addCoffee = async function(name) {
        coffeeList += (coffeeList ? ',': '') + await addCoffee(name);
    };
    
    await _addCoffee('에스프레소');
    console.log(coffeeList);
    await _addCoffee('아메리카노');
    console.log(coffeeList);
    await _addCoffee('카페모카');
    console.log(coffeeList);
};
coffeeMaker();
```