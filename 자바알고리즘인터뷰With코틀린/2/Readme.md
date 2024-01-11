# 2장 자바, 세상에서 가장 유명한 언어
### 자바의 역사
생략
### 자바의 특징
OOP의 4가지 특징에 대해 설명(캡슐화, 추상화, 상속, 다형성) <br>
이 외에 접근 제어자, 제너릭, 람다, 함수형 인터페이스, 스트림 API등 간단히 서술되어 있음(정리 생략)
### 자바의 도구
자바 9 버전에서 JShell이 처음 등장, REPL(Read-Eval-Print-Loop)환경을 사용할 수 있게 되었음<br> 
-> 입력하고 실행 후 출력하는 과정을 반복하면서 결과를 **실시간**으로 확인할 수 있는 환경을 의미
```
--터미널 환경에서 jshell입력 시 간단히 실행
$ jshell

jshell> int a = 7
7

--import도 가능(기본적인 패키지는 이미 임포트되어있음)
jshell> import ...

--메소드 선언도 가능
jshell> int square(int a) {
            return a * a;        
        }
created method square(int)

jshell> square(7)
49
```
