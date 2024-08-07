# 34장 int 상수 대신 열거 타입을 사용하라
#### 정수 열거 패턴 (취약한 코드)
```
public static final int APPLE_FUJI = 0;
public static final int APPLE_PIPPIN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int APPLE_NAVEL = 0;
public static final int APPLE_TEMPLE = 1;
public static final int APPLE_BLOOD = 2;
```
* 타입을 보장할 방법이 없으며 표현력도 좋지 않다.

#### 단순한 열거 패턴
```
public enum Apple {  FUJI, PIPPIN, GRANNY_SMITH, NAME }
public enum Orange { NAVEL, TEMPLE, BLOOD, NAME }
```
* 자바의 열거 타입은 **완전한 형태의 클래스**라서 단순한 정수값인 다른 언어의 열거 타입보다 강력하다.
* 상수 하나당 자신의 인스턴스를 하나씩 만들어 **public static final** 필드로 공개한다.
  * 인스턴가 통제된다.
  * 싱글턴을 일반화한 형태
* 컴파일 타입 안정성을 제공한다.
  * 정수 열거 패턴과 달리 Apple과 Orange를 동등 연산자(==)로 비교하면 **컴파일 오류**가 발생
* 열거 타입은 각자의 이름공간이 있어 이름이 같은 상수도 공존 가능하다.
  * ex) Apple의 Name과 Orange의 Name
* 열거 타입의 toString 메서드(생략가능)는 출력하기 적합한 문자열을 반환한다.
  * System.out.println(Apple.FUJI)  // =>  FUJI 

