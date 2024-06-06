# 6장 프로토타입
* 자바스크립트는 프로토타입 기반 언어이다.
* 클래스 기반 언어에서는 상속을 사용하지만 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻는다.

### 프로토타입의 개념 이해
#### 프로토타입 도식 (출처: 정재남, "코어 자바스크립트", 위키북스(2021))
<img src = "./프로토타입 도식.png" alt = "프로토타입 도식"></img>
* Constructor를 new 연산자와 함꼐 호출하면 새로운 instance가 생성된다.
* 이때 instance에는 __proto__라는 프로퍼티가 자동으로 부여되는데 Constructor의 prototype이라는 프로퍼티를 참조한다.
* prototype은 객체이고 이를 참조하는 __proto__역시 객체이다.
* prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장한다.
* 그러면 인스턴스에서도 숨겨진 프로퍼티인 __proto__를 통해 이 메서드들에게 접근할 수 있게 된다.
> __proto__는 레거시 코드에 대한 호환성 유지를 위해서만 존재할뿐 실무에서는 가급적 __proto__를 사용하지 않기를 권장<br>
> 대신, Object.getPrototypeOf() / Object.create() 등을 이용하면 된다.

#### 코드를 통한 예제
```
var Person = function(name) {
    this._name = name;
};

Person.prototype.getName = function() {
    return this._name;
};

var suzi = new Person('Suzi');
console.log(suzi.__proto__.getName()); //undefined
suzi.__proto__._name = 'SUZI__proto__';
console.log(suzi.__proto__.getName());  //SUZI__proto__

//proto를 생략
console.log(suzi.getName()); //Suzi
```
* 에러가 아닌 undefined가 나오는 것은 호출할 수 있는 함수라는 의미
* 메서드로써 함수를 호출할 경우 this는 메서드명 바로 앞 객체이므로 suzi.__proto__가 된다.
* 그러므로 name을 할당하면 출력이 잘된다. (관건은 this)
* __proto__는 생략 가능한 프로퍼티이므로 suzi.getName()은 this가 suzi가 되므로 Suzi가 잘 출력된다.

#### 프로토타입 도식2 (출처: 정재남, "코어 자바스크립트", 위키북스(2021))
<img src = "./프로토타입 도식2.png" alt = "프로토타입 도식"></img>
> 즉, 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 **인스턴스**에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 **접근할 수 있다.**

#### 대표적인 내장 생성자 함수 Array
```
var arr = [1, 2];
Array.isArray(arr);         //true
arr.isArray();              //X -> TypeError
arr.push(1)                  //O 가능
```
* Array에서 isArray()함수는 프로퍼티내부에 있지 않아서 인스턴스가 직접 호출할 수 없다.
* 하지만 push()함수는 직접 호출이 가능하다.

#### constructor 프로퍼티
* 생성자 함수의 프로퍼티인 prototype 객체 내부에는 constructor라는 프로퍼티가 있다. (인스턴스의 __proto__도 마찬가지)
* 이 프로퍼티는 생성자 함수(자기 자신)을 참조한다.
* 인스턴스로부터 그 원형이 무엇인지를 알 수 있는 수단이 된다. (항상 안전하진 않지만 클래스 상속의 흉내가 가능해짐)
```
var arr = [1,2]
Array.prototype.constructor === Array   //true
arr.__proto__.constructor === Array     //true
arr.constructor === Array               //true
```

### 프로토타입 체인
#### 메서드 오버라이드
* prototype객체를 참조하는 __proto__를 생략하면 인스턴스는 prototype에 정의된 프로퍼티나 메서드를 마치 자신의 것처럼 사용할 수 있다.
* 만약 인스턴스가 동일한 이름의 프로퍼티나 메서드를 가지고 있다면 가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없다면 그 다음 가까운 대상을 검색한다.
* this가 prototype이 아닌 인스턴스를 바라보게 바꿔주려면 call이나 apply를 사용하면 된다.
```
console.log(iu.__proto__.getName.call(iu))
```

#### 프로토타입 체인
* 어떤 데이터의 __proto__프로퍼티 내부에 다시 __proto__프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인이라 하고, 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 한다.
* 프로토타입 체이닝을 통해 각 프로토타입 메서드를 자신의 것처럼 호출할 수 있다.