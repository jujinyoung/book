# 2장 실행 컨텍스트
### 실행 컨텍스트란?
* **실행할 코드에 제공할 환경 정보들을 모아놓은 객체**로 일반적으로 함수를 실행할 때 구성된다.
* 동일한 환경정보를 가진 컨테스트를 구성하고, 이를 콜스택위에 쌓아올렸다가 가장 위에 있는 컨텍스트와 관련있는 코드들을 실행하는 식

#### 실행 컨텍스트에 담기는 내용들
1. VariableEnvironment
   * 현재 컨텍스트 내의 식별자들에 대한 정보
   * 선언 시점의 LexicalEnvironment의 스냅샷으로 변경 사항은 반영되지 않는다.
2. LexicalEnvironment
   * 처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영됨.
3. ThisBinding
   * this 식별자가 바라봐야할 대상 객체  

### VariableEnvironment
* 실행 컨텍스트 생성시에 VariableEnvironment에 정보를 먼저 담은 후 LexicalEnvironment에 복사한 후에 주로 LexicalEnvironment를 활용 

### LexicalEnvironment
* 컨텍스트를 구성하는 환경 정보들을 사전에 접하는 느낌으로 모아둔 것(저자 피셜)
* 이곳에는 크게 environmentRecord와 outerEnvironmentReference 두가지의 정보가 담긴다.

### EnvironmentRecord
* 현재 **컨텍스트**와 관련된 **코드의 식별자 정보**들이 저장된다.
* 매개변수 식별자, 선언한 함수, var로 선언된 변수의 식별자 등 (let, const 제외)
* 컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가며 순서대로 수집한다.

### 호이스팅(hoisting)
* EnvironmentRecord에는 현재 컨텍스트의 식별자 정보가 저장된다. 
* 그렇기 때문에 함수가 실행되지 않아도 식별자 정보들은 수집이 된다.
* 호이스팅은 **식별자들을 최상단으로 끌어올린 다음** 실제 코드를 실행하는 것과 유사하다.

#### 호이스팅 원본 코드
```
function a () {
   var x = 1;        // 수집 1
   console.log(x);   // (1)
   var x;            // 수집 2
   console.log(x);   // (2)
   var x = 2;        // 수집 3
   console.log(x);   // (3)
}
```
예상 결과 <br>
(1) 1 <br>
(2) undefined <br>
(3) 2

실제 결과 <br>
(1) 1 <br>
(2) 1 <br>
(3) 2

#### 호이스팅을 마친 코드
```
function a () {
   var x;         // 수집 1
   var x;         // 수집 2
   var x;         // 수집 3
   
   x = 1;         // 수집 1 할당부분
   console.log(x) // (1)
   console.log(x) // (2)
   x = 2;
   console.log(x) // (3)
}
```
* **실제 엔진은 이러한 변환과정을 거치지 않는다.**

#### 함수 선언의 호이스팅 - 전
```
function a(){
    console.log(b);  // (1)
    var b = 'bbb';
    console.log(b);  // (2)
    function b() { }
    console.log(b);  // (3)
};

a();
```
#### 함수 선언의 호이스팅 - 후
```
function a(){
    var b; //var b = 'bbb' 선언부
    function b() { } //함수 선언 전체 (var b = function b() {}와 동일)

    console.log(b);  // (1)
    b = 'bbb'; //var b = 'bbb' 할당부
    console.log(b);  // (2)
    console.log(b);  // (3)
};

a();
```
결과 <br>
(1) [Function: b]
(2) 'bbb'
(3) 'bbb'

### 함수 선언문과 함수 표현식
#### 함수를 선언하는 3가지 방식
```
function a () {}  //함수 선언문. 함수명 a가 곧 변수명
a();  // 실행 ok

var b = function () {}  //(익명) 함수 표현식. 변수명 b가 곧 함수명.
b();  // 실행 ok

var c = function d () {}   //기명 함수 표현식. 변수명은 c, 함수명은 d.
c();  // 실행 ok
d();  // 에러!   -> 외부에서 함수명을 호출할 수 없음
```
* 함수 선언문
  * function 정의부만 존재. 별도의 할당 명령이 없다.
  * 반드시 함수명이 정의되어야 한다.
* 함수 표현식
  * 정의한 function을 별도의 변수에 할당한다.
  * 함수명이 없어도 된다.
    * 함수명 정의 O -> 기명 함수 표현식
    * 함수명 정의 X -> 익명 함수 표현식 (일반적)

#### 함수 선언문과 함수 표현식의 호이스팅 전
```
console.log(sum(1,2));
console.log(multiply(3,4));

function sum(a, b) {             //함수 선언문 sum
   return a + b;
}

var multiply = function(a, b) {  //함수 표현식 multiply
   return a + b;
}
```
#### 호이스팅 후
```
var sum = function sum(a, b) {   //함수 선언문은 전체를 호이스팅
   return a + b;
};
var multiply;                    //변수는 선언부만 끌어올림

console.log(sum(1,2));
console.log(multiply(3,4));

multiply = function(a, b) {      //변수의 할당부는 원래 자리
   return a + b;
}
```
결과
> 3 <br>
> multiply is not a function
* 만약 동명의 함수를 선언하게 된다면 함수 선언문보다는 함수 표현식이 더 안전하다.
* 함수 선언문은 선언부 전체가 호이스팅되기 때문에 마지막에 오는 함수가 덮어씌워진다.

### 스코프, 스코프 체인, outerEnvironmentReference
#### 스코프
* 식별자에 대한 유효범위

#### 스코프 체인
* '식별자의 유효범위'를 안에서부터 바깥으로 차례로 검색해나가는 것
* outerEnvironmentReference가 이를 가능하게 한다.
  * outerEnvironmentReference는 현재 호출된 함수가 선언될 당시의 LexicalEnvironment를 참조한다.

#### 스코프 체인의 예시
```
var a = 1;
var outer = function() {
   var inner = function() {
      console.log(a);   // 호이스팅에 의해 undefined
      var a = 3;
   };
   inner();
   console.log(a);      // 1
};
outer();
console.log(a);         // 1
```
* 변수 은닉화: inner함수 내부에서 a를 선언했기 때문에 전역변수 a에 접근할 수 없음