# 5장 클로저
#### 클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달(return, 콜백)할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상

```
var outer = function () {
    var a = 1;
    var inner = function () {
        console.log(++a);
    };
    return inner;
};
var outer2 = outer();
console.log(outer2());  // 2
console.log(outer2());  // 3
```
* GC의 동작방식은 어떤 값을 참조하는 변수가 하나라도 있으면 그 값은 수집대상이 안된다.
* outer는 inner함수를 반환하는 함수이다. outer함수의 실행 컨텍스트가 종료될 때, outer2 변수는 inner함수를 참조하게 된다.

### 클로저와 메모리 관리
```
var outer = (function () {
    var a = 1;
    var inner = function () {
        return ++a;
    };
    return inner;
})();
console.log(outer());   // 2
console.log(outer());   // 3
outer = null;       //outer 식별자의 inner 함수 참조를 끊음
```
* 클로저는 GC의 수거대상이 되지 않는 현상을 말하기 때문에, 메모리 누수에 대한 문제가 존재한다.
* 클로저의 필요성이 사라진 시점에 식별자에 참조형이 아닌 기본형(null, undefined)을 할당하면 된다.

### 클로저의 활용사례
#### 1. 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때
```
var fruits = ['apple', 'banana', 'peach'];
var $ul = document.createElement('ul');     // (공통 코드)

var alertFruitBuilder = function (fruit) {
  return function () {
    alert('your choice is ' + fruit);
  };
};

fruits.forEach(function (fruit)) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListener('click', alertFruitBuilder(fruit));
  $ul.appendChild($li);
}
```

#### 2. 접근 권한 제어(정보 은닉)
* 클로저를 사용하면 함수 차원에서 public한 값과 private한 값을 구분하는 것이 가능하다.
* 외부에 제공하고자 하는 정보들을 모아서 return하고, 내부에서만 사용하는 정보들은 return하지 않는 것으로 접근 권한을 제어한다.

#### 3. 부분 적용 함수
* 부분 적용 함수란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘기고, 나중에 (n-m) 개의 인자를 넘겨 함수 실행 결과를 얻을 수 있는 함수이다.
* 미리 일부 인자를 넘겨두어 기억하게끔 하고 추후 필요한 시점에 기억했던 인자들까지 함께 실행하게 하는 것이 클로저의 정의에 부합하다.
* ex) 디바운스: 짧은 시간 동안 동일한 이벤트가 많이 발생할 경우 이를 전부 처리하지 않고 처음 또는 마지막에 발생한 이벤트에 대해 한 번만 처리하는 것

#### 4. 커링 함수
여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것
```
var getInfomation = function (baseUrl) {
    return function (path) {
        return function (id) {
            return fetch(baseUrl + path + id);
        };
    };
};
//ES6에서는 화살표로 표현
var getInformation = baseUrl => path => id => fetch(baseUrl + path + '/' + id);
```
* 마지막 인자가 전달되기 전까지는 원본 함수가 실행 되지 않는다. -> 지연 실행시 유리