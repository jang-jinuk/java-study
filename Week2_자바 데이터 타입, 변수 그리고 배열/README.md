# **자바 데이터 타입, 변수 그리고 배열**

## Index

- 프리미티브 타입 종류와 값의 범위 그리고 기본 값
- 프리미티브 타입과 레퍼런스 타입
- 리터럴
- 변수 선언 및 초기화하는 방법
- 변수의 스코프와 라이프타임
- 타입 변환, 캐스팅 그리고 타입 프로모션
- 1차 및 2차 배열 선언하기
- 타입 추론, var

---

## **프리미티브 타입** 종류와 값의 범위 그리고 기본 값

Java에서는 **Primitive Type(원시 타입)** 은 다음과 같은 종류가 있다.

| 자료형 | 비트 크기 | 값 범위 | 기본 값 | 특징 |
| --- | --- | --- | --- | --- |
| byte | 8bit | -128 ~ 127 | 0 | 메모리 절약, 코드 명확성 용도 |
| short | 16bit | -32,768 ~ 32,767 | 0 | 대용량 배열에서 메모리 절약 |
| int | 32bit | -2,147,483,648 ~ 2,147,483,647 | 0 | Java8 이후 unsigned 지원: 0 ~ 2^32-1 (Integer 클래스 활용) |
| long | 64bit | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 | 0L | Java 8 이후 unsigned 지원: 0 ~ 2^64-1 (Long 클래스 활용) |
| float | 32bit | 약 ±3.40282347×10³⁸ | 0.0f | 금액처럼 정확성을 요구하는 데이터에 사용 x |
| double | 64bit | 약 ±1.79769313486231570×10³⁰⁸ | 0.0d | 금액처럼 정확성을 요구하는 데이터에 사용 |
| boolean | - | true / false | false | true, false 값만 존재 |
| char | 16bit | ‘\u0000’ (0) ~ ‘\uffff’ (65,535) | '\u0000’ | 유니코드 문자 표현 |

## 프리미티브 타입과 레퍼런스 타입

Java의 데이터타입은 **Primitive Type(원시 타입)** 과 **Reference Type(참조 타입)** 두가지로 나뉜다.

8가지의 모든 **Primitive Type**을 제외하면 데이터 타입은 모두 **Reference Type**이다.

Reference Type은 `new` 키워드 생성된 배열, 객체(인터페이스, 열거형, String…)가 포함된다.

두가지 타입은 저장 되는 위치도 상이하다.

Primitive Type의 경우 JVM Memory에서 Stack 영역에 저장되게 된다.

반면, Reference Type의 경우에는 Heap 영역에 저장된다.

위에서 말한 것처럼 Primitive Type의 경우 기본값이 있듯이 Reference Type는 값이 없을 경우 모두 `null` 로 초기화된다. (Primitive Type의 경우 null을 할당할 경우 컴파일 에러가 발생한다.)

비교연산을 통해 비교할 때도 타입마다 동작이 다르다.

**Primitive Type의 경우**

- `==` 연산자로 **값** 자체를 비교한다.

**Reference Type의 경우**

- `==` 연산자로 주소(참조값)를 비교한다.(동일성 비교)
- 값 비교는 `equals()` 메서드 사용한다.(동등성 비교)

### 각 변수마다 기본값 할당 시점

1. 클래스 변수(static 필드)
    - Class Loader가 클래스를 Linking할 때 기본값이 할당된다.(정확히는 Linking 과정 중 `Preparation` 때 할당된다.)
2. 인스턴스 변수(필드)
    - 객체 생성 시점에 기본값이 할당된다.
3. 지역 변수(로컬 변수)
    - 기본값이 자동으로 할당되지 않는다.
    - 반드시 명시적으로 초기화가 필요하다.

---

## 리터럴

Literal 이란, 직접 작성한 고정된 값을 말한다.

### 정수 리터럴

```java
int decimal = 100; // 10진수
int binary = 0b1010; // 2진수
int octal = 0144; // 8진수
int hex = 0x64; // 16진수

long longValue = 100L; // long형 리터럴
```

### 실수 리터럴

```java
float f1 = 3.14f; // float형
double d1 = 3.14; // double형 (기본 실수 타입)
double d2 = 1.23e2; // 지수표현 (1.23 x 10² = 123.0)
```

### 문자 리터럴

```java
char c1 = 'A'; // 문자
char c2 = '\n'; // 특수문자 (줄바꿈)
char c3 = '\u0041'; // 유니코드 (A)
```

### 문자열 리터럴

```java
String s1 = "Hellow";
String s2 = "Java\nWorld"; // 줄바꿈 포함
```

### 논리 리터럴

```java
boolean t = true;
boolean f = false;
```

---

## 변수 선언 및 초기화하는 방법

### 변수 선언

변수 선언은 프로그램에서 값을 저장할 공간을 만드는 것이다.

```java
타입 변수명;
```

```java
int age;
int age, number, size; // 같은 타입의 여러개 변수를 선언할 수 있다.
```

### 변수 초기화

초기화는 변수에 처음으로 값을 대입하는 것이다.

```java
// 변수 선언 후 초기화
int age;
age = 25; 
```

```java
// 변수를 한번에 선언과 초기화
int age = 25;
```

```java
// 여러개 변수를 한번에 선언과 초기화
int age = 20, number = 123, size = 100;
```

### 지역 변수는 반드시 초기화해야 함

```java
public class Example {
    public static void main(String[] args) {
        int num;
        // System.out.println(num); 컴파일 에러(초기화 안 됨)
        num = 10;
        System.out.println(num);  // 정상 작동
    }
}
```

### 상수 (final) 선언

```java
final double PI = 3.14159;
```

변수는 나중에 값이 바뀔 수 있지만, 상수는 한번 값을 정하면 바꿀 수 없다.(상수 변수명은 `대문자`와 `_` 를 넣어 선언한다.)

## 변수의 스코프와 라이프타임

### 스코프

| 종류 | 선언 위치 | 접근 가능 범위 |
| --- | --- | --- |
| 로컬 변수(Local Variable) | 메서드나 블록 내부 | 선언된 블록 { } 안에서만 사용 가능 |
| 인스턴스 변수(Instance Variable) | 클래스 내부, 메서드 밖 | 객체가 존재하는 동안 접근 가능 (this.변수명) |
| 클래스 변수(Static Variable) | static 키워드로 선언 | 프로그램 실행 동안 공용으로 접근 가능(클래스명.변수명) |

```java
public class ScopeExample {
	int instanceVar = 10; // 인스턴스 변수
	static int staticVar = 20; // 클래스 변수(정적 변수)
	
	public void testMethod() {
		int localVar = 30;
		System.out.println(localVar); // 접근 가능
		System.out.println(instanceVar); // 접근 가능
		System.out.println(staticVar); // 접근 가능
	}
	
	public void anotherMethod() {
		// System.out.println(localVar); 접근 불가능
	}
}
```

### 라이프타임

라이프타임은 변수가 메모리에 존재하는 기간을 말한다.

| 변수 종류 | 생성 시점 | 소멸 시점 | 메모리 영역 |
| --- | --- | --- | --- |
| 로컬 변수(Local Variable) | 블록 실행 시 | 블록 종료 시 | Stack |
| 인스턴스 변수(Instance Variable) | 객체 생성 시 | 객체 소멸 시(G.C가 수거할 때) | Heap |
| 클래스 변수(Static Variable) | 프로그램 시작 시 | 프로그램 종료 시 | Method Area |

## 타입 변환, 캐스팅 그리고 타입 프로모션

타입 변환은 서로 다른 자료 형 간에 값을 바꾸는 것을 말한다.

자바에서는 두가지의 타입 변환이 있다.

- **자동 형 변환(묵시적 변환):** 작은 타입 → 큰 타입 (예: int → long, float → double)
- **명시적 형 변환(강제 캐스팅):** 큰 타입 → 작은 타입(데이터 손실 가능) (예: double → int, long → short)

### 자동 형 변환

자바는 데이터 손실이 없는 경우 자동으로 타입을 변환해 준다.

```java
int num = 10;
double result = num;
```

```java
byte -> short -> int -> long -> float -> double
          ↑
	       char
```

*화살표 방향대로 자동 형 변환이 가능하다.

### 명시적 형 변환

큰 타입에서 작은 타입으로 형 변환할 때는 자동 형 변환이 되지 않기 때문에 명시적으로 형 변환이 필요하다.

```java
int number = 10;
byte result = (byte)number;
```

### 타입 프로모션

자바는 연산 시, 피연산자의 타입을 맞춰야하기 때문에 큰 타입으로 자동 형 변환을 한다.

**int + double**

```java
int num1 = 10;
double num2 = 12.1;
double result = num1 + num2; // num1이 double으로 변환
```

**byte + short**

```java
byte x = 10;
short y = 20;
int result = x + y; // x, y가 int로 변환
```

**문자와 숫자 연산**

```java
char c = 'A';
int result = c + 1 // int로 변환(result는 66)
```

---

## 1차 및 2차 배열 선언하기

### 1차 배열

**선언**

```java
int[] arr = new int[5]; 크기가 5인 배열 선언
```

**초기화**

```java
arr[0] = 1;
arr[1] = 2;
arr[4] = 5;
```

**선언과 초기화**

```java
int[] scores = {50, 80, 20, 10, 90};
```

### 2차 배열

**선언**

```java
int[][]arr = new int[3][3];
```

**초기화**

```java
arr[0][0] = 30;
arr[3][2] = 20
```

**선언과 초기화**

```java
int[][] scores = 
{
	{10, 20, 30},
	{50, 10, 40},
	{30, 25, 21}
};
```

---

## 타입 추론, var

### 타임 추론이란?

컴파일러가 변수의 타입을 코드로 추론하는 것을 말한다.

```java
String name = "장진욱";
```

다음과 같은 경우는 String이라고 타입 명시되어 있다.

하지만 타입 추론을 사용하면 이렇게 선언할 수 있다.

### var

```java
var name = "장진욱"; // String으로 추론됨
```

다음과 같이 **var** 키워드를 사용하기 위해서는 몇가지 주의할 점이 있다.

- 지역 변수만 가능
- 반드시 초기화 필요
- null 초기화 불가
- 타입이 명확해야 함(명확하지 않는 제네릭 같은 경우 var 키워드를 지양해야하는게 좋음)

> Reference
> - [Primitive Data Types (The Java™ Tutorials > Learning the Java Language > Language Basics)](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)


