# JVM의 소스파일 실행 과정 이해하기

## Index
- JVM이란 무엇인가
- 컴파일 하는 방법
- 실행하는 방법
- 바이트코드란 무엇인가
- JIT 컴파일러란 무엇이며 어떻게 동작하는지
- JVM 구성 요소
- JDK와 JRE의 차이

## JVM이란 무엇인가?

JVM은 Java Virtual Machine 으로 바이트 코드를 실행하는 역할을한다. 인터프린터와 JIT(Just In Time) 컴파일러 방식으로 실행하며 동일한 바이트 코드를 JVM을 통해 다양한 OS위에서 실행 될 수 있다.

### JVM의 특성

- 스택 기반의 가상 머신
- 가비지 컬렉션 사용
- 플랫폼 독립성 보장

## 컴파일 하는 방법

우선, 자바 소스 코드를 작성한다.

```java
public class Main {
  public static void main(String[] args) {
    System.out.println("Hello, Java");
  }
}
```

작성한 자바 `.java(자바 소스코드)`를 `javac` 명령해 컴파일러를 통해 `바이트 코드`로 컴파일 해준다.

그럼 컴파일 된 `(파일명).class` 파일이 생성된다.

```bash
javac Main.java
```

javac를 했을 때 컴파일러는 내부적으로 다음과 같이 동작한다.

1. 어휘 분석
    1. 자바 소스코드를 문자를 읽어 최소 토큰 단위로 나눈다.
    2. ex) `public` , `int` , `{` , `for` , `;`
2. 구문 분석
    1. 나누어진 토큰들을 자바 문법에 맞는지 확인한다.
    2. ex) `int a = 10` 다음과 같이 세미 콜론이 빠진 거 같은 문법 오류를 찾아낸다.
    3. 토큰들의 문법 구조가 이상없는지 확인하고, 이를  추상 구문 트리(AST,  Abstract Syntax Tree) 자료구조 형태로 만든다.
3. 의미 분석
    1. 만들어진 **AST**에 구문들의 **타입을 확인**한다.
    2. ex) `int a = "string";` 다음과 같이 `int` 타입 변수에 `String`을 할당하지 않았는지 타입 확인을 한다.
    3. 기호 테이블에 변수 클래스, 메서드 이름과 그 속성(타입, 범위)을 기록하고 관리한다.
    4. 이 과정을 모든 마치면 컴파일러과 소스 코드를 의미적으로 완전히 이해할 수 있게 된다.
4. 바이트 코드 생성
    1. 의미 분석이 완료된 AST를 순회하면서 JVM이 이해할 수 있는 명령어 집합인 바이트 코드로 변환을 시킨다.
    2. 모두 변환이 완료되면, 최종적으로 `.class` 파일이 완성된다.
## 실행하는 방법

실행 방법은 다음과 같다.

```bash
java Main
```

`java` 명령어를 통해 `.class`  파일을 실행해주면되는데, 이때 뒤에 확장자명(.class)은 생략한다.

## 바이트코드란 무엇인가?

바이트코드는 JVM이 실행하는 명령어의 형태를 말한다. 바이트코드는 소스코드와 기계어 사이에 있는 중간 언어이다.

자바의 철학 중 하나인, “**Write Once, Run Anywhere”** 를 뒷바침 해줄 수 있는 것이 바로 바이트코드와 JVM이 있기 때문이다.

예를 들어 C언어 같은 경우 컴파일하면 특정 OS에 맞춰진 기계어 코드가 컴파일된다.

하지만 java 바이트코드의 경우 OS의 관계없이 생성되고, JVM을 통해 해석되기 때문에 JVM만 설치되어 있는 곳이라면 어디서든지 실행이 가능해진다.

JDK 도구 중 `javap` 는 `.class` 을 역어셈블하여 바이트코드를 확인할 수 있게 해준다.

```bash
javap -c Main
```

`-c` 속성과 같이 `javap` 를 실행해주면 바이트코드도 함께 출력해준다.

```bash
Compiled from "Main.java"
public class Main {
  public Main();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #13                 // String Hello, Java
       5: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

## JIT 컴파일러란 무엇이며 어떻게 동작하는지

JIT 컴파일러는 Just In Time의 줄임말로 바이트 코드를 실행하는 Execution Engine 중 하나이다.

JVM은 바이트코드를 실행할 때 인터프리터와 JIT 컴파일러를 함께 사용한다.

평소에는 인터프리터를 통해 바이트 코드를 하나를 해석하고 바로 실행하는 방식으로 바이트 코드를 실행시킵니다.

근데 실행도중 자주 실행되는 코드가 발생하면 JIT 컴파일러가 자주 발생한 코드를 바로 네이티브 기계어로 변환한뒤 캐싱하여 저장하여, 다음 동일한 코드가 호출될 때 인터프리터 방식을 버리고 저장된 네이티브 기계어를 CPU로 바로 보내 빠르게 실행시킨다.

네이티브 기계어로 컴파일된 코드는 컴파일되는 속도는 인터프리터에 비해 시간이 많이 걸리지만, 실행 속도에서는 압도적으로 빠른속도를 가진다.


>💡 HotSpot JVM에서는 다음과 같이 자주 발생하는 코드에 대해서 단계적으로 관리한다.

>**계층형 컴파일의 레벨**
> - Level 0 : 인터프리터
    - 모든 코드는 처음 인터프리터를 통해 실행이된다.
    - 이 때 프로파일링하며 호출횟수, 루프 반복횟수등을 수집한다.
> - Level 1 : C1 컴파일러(Client Compiler)
    - 동일하게 약 1,500번 호출된 코드(Warm Code)가 발생하면 C1 컴파일러를 통해 네이티브 코드로 컴파일된다.
    - C1 컴파일러는 복잡한 최적화 과정을 생략하여 비교적 빠른 컴파일이 가능하다.
> - Level 2 & 3: C1  컴파일러 + 추가 프로파일링
    - Level 1에서 컴파일된 코드를 실행하면서, 추가적으로 상세한 포로파일링을 한다.
> - Level 4: C2 컴파일러(Server Compiler)
    - 동일하게 약 10,000번 호출된 코드(Hot Code)가 발생하면 C2 컴파일러를 통해 네이티브 컴파일러로 컴파일된다.
    - C2 컴파일러는 최대한 최적화를 하여 네이티브 기계어로 컴파일하기 때문에 속도가 느리지만, C1 컴파일러로 컴파일한 코드보다 훨씬 빠르다.

## JVM 구성 요소

![Screenshot 2025-10-22 at 2.55.12 PM.png](https://mudhub-bucket.s3.ap-northeast-2.amazonaws.com/gist/Screenshot+2025-10-22+at+2.55.12%E2%80%AFPM.png)

### **Class Loader Sub System**

**Class Loader의 동작 과정**

- **Loading:** `.class` 파일을 읽고 메타데이터를 Method Area에 저장한다.
    - Heap에 로드된 클래스를 나타내는 `Class` 객체를 생성한다.
- **Linking:** 로드된 클래스를 실행 준비 상태로 만드는 역할을 한다.
    - `Verification(검증)`: 바이트코드가 JVM 규칙을 따르고 안전하게 실행될 수 있는지 확인
    - `Preparation(준비)`: 정적 변수에 메모리를 할당하고 기본값을 부여
    - `Resolution(해석)`: 심볼릭 참조를 메모리의 직접 참조로 변환


    >✔️ 심볼릭 참조: 직접적인 메모리 주소 대신, 이름으로 참조하는 방식
    
    ```java
    CONSTANT_Class_info: java/lang/String
    CONSTANT_Methodref_info: java/lang/String.length:()I
    ```

- **Initialization:** 정적변수에 실제 값을 할당하고, 클래스에 정의된 static 블록을 실행한다.

**Class Loader 종류**

- **Bootstrap Class Loader**: 핵심 Java 클래스(`JAVA_HOME/lib`)를 로드한다.

>✔️ `JAVA_HOME/lib`: Java Java 프로그램이 기본적으로 의존하는 핵심 표준 클래스들이 담겨 있습니다. 예를들어 String, 컬렉션 프레임워크, 입출력, 네트워킹, 쓰레딩 리플렉션 같은 기본 기능 패키들이 포함되었다.


- **Extension Class Loader:** 확장 디렉터리 `Java_HOME/jre/lib/ext` 에서 클래스를 로드한다.


>💡 **Java 9 이후 모듈 시스템**이 도입으로 확장 디렉터리에 있던 확장 라이브러리는 일반 클래스나 모듈 경로 관리되고 있어, 사실상  Extension Class Loader와 ext 디렉터리는 사실상 폐지되었다.

>✔️ 확장 디렉터리: 표준 라이브러리(`java.lang`, `java.util` 등)를 확장하는 확장 라이브러리 JAR가 담겨 있다.

- **System/Application Class Loader:** application classpath에 있는 클래스를 로드한다.

>✔️ application classpath에는 프로젝트가 실행 시 참조하는 모든 클래스(직접 작성한 애플리케이션 코드와 서드파티 라이브러리 JAR 등)가 있다.

</aside>

### JVM Memory Areas

1. **Shared Areas: 모든 스레드가 공유하는 영역**

   **Method Area**

    - 클래스 로드가 로드한 클래스 메타데이터를 저장한다.

   **Heap**

    - `new` 키워드로 생성된 모든 객체와 배열이 저장되는 공간입니다.
    - `G.C`가 관리하는 주된 영역입니다.

1. **Thread-Specific Areas: 스레드별로 개별 생성되는 영역**

   **Stack**

    - 메서드 호출과 관련된 정보들이 저장되는 공간이다.
    - 메서드가 호출되면, 메서드를 위한 Stack Frame(스택 프레임)이 생성되고 스택에 push 된다.
    - 스택 프레임 내부에는 지역변수, 중간 연산 결과 등이 저장된다.
    - 메서드 호출이 끝나면, 스택 프레임은 스택에서 pop 되어 사라진다.

**PC Registers**

- 현재 스레드가 실행하고 있는 JVM 명령어(바이트코드) 주소를 Program Counter라 하는데 이것을 저장하는 공간이다.
- 여러 스레드가 동시에 실행할 때(Context Switching) CPU에게 다음작업이 어떤 것인지 알려준다.

**Native Method Stacks**

- 자바 코드가 아닌 C, C++ 언어 같은 네이티브 코드를 호출할 때 사용되는 전용 스택이다.

### Execution Engine

1. **Interpreter**
    - JVM 바이트코드을 한 줄씩 읽어서 즉시 해석하고 실행한다.
    - 바이트코드 한 줄씩 네이티브 작업(기계어 코드로 변환)을 한다.
    - 바로 해석하고 실행하기 때문에 초기 구동 속도가 빠르다라는 장점이 있지만, 반복되는 코드도 동일하게 한 줄씩 해석하고 실행하기 때문에 전반적으로 느리다.
2. **JIT Compiler**
    - 인터프리터의 속도를 보안하기 위해 반복적으로 호출되는 메소드, 루프를 하나로 묶어 네이티브 작업을 한다.
    - 하나로 묶어 네이티브 기계어로 변환된 데이터는 캐싱되어 속도를 높혀준다.

1. **Garbage Collector**
    - Heap 영역에서 더 이상 사용하지 않는 객체를 메모리에서 해제하는 작업을 한다.
    - GC가 객체를 제거하는 기준은 더 이상 객체를 참조하지 않을 때이다.
    - 애플리케이션 스레드와 함께 동시에 별도의 스레드로 실행된다.

### JNI(Java Native Interface)

- JNI는 하드 웨어 제어가 필요할 때 JVM에서 네이티브 메서드(C/C++ 라이브러리)를 사용할 수 있도록 이어주는 인터페이스다.
- JNI는 자바와 C언어 서로 부를 때 필요한 모든 접속 방법을 정의한 명세이다.

### Native Method Library

- 네이티브 메서드를 실행하기 위한 라이브러리이다.
- C/C++ 같은 언어로 작성된 라이브러리를 포함한다.

## JDK와 JRE의 차이

![JDK, JRE, JVM](https://mudhub-bucket.s3.ap-northeast-2.amazonaws.com/gist/Screenshot+2025-10-22+at+5.17.45%E2%80%AFPM.png)

### JDK
- JDK는 Java Development Kit의 약자로, 자바를 통해 개발할 수 있는 환경을 말한다.
- 개발에 필요한 라이브러리들과 `javac`, `jar`, `javadoc`, `javap`등의 개발 도구를 포함하고 있다.
- 개발에는 작성한 코드를 실행이 필요하기 때문에 JRE를 포함하고 있다.
### JRE
- JRE는 Java Runtime Enviroment의 약자로, 자바 바이트코드가 실행할 수 있는 환경을 말한다.
- 바이트코드를 실행하기 위해 필요한 라이브러리를 API와 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일(jar 파일)이 있다.
- JRE는 JVM을 포함하고 있다.

> Reference
>- [Java virtual machine](https://en.wikipedia.org/wiki/Java_virtual_machine)
>- [How work JVM](https://www.geeksforgeeks.org/java/how-jvm-works-jvm-architecture/)
