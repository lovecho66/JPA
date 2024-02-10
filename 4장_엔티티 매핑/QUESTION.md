# 개념 정리 / 질문 및 답변

## 개념 정리

### 1.final
  - final 키워드는 변수,메서드,클래스에 사용할 수 있다.
  1. final 변수 : 해당 변수는 더이상 재할당 할 수 없다.
      1. 첫 초기화 이후 재할당이 불가능해진다.
      ```java
      final int number = 1;
      number = 2; // Cannot assign a value to final variable 'number'
      ```
      - 재할당 시도 시 컴파일 에러가 발생
        
      2. final 변수 초기화 방법 2가지가 있다.<br>
        2-1. 초기화할 값이 단순한 값이면 __클래스의 필드에 선언__ 한다.
  
          ```java
          class Car {
            private static final int MOVE_STEP = 1;
            // ...
          }
          ```
          
          - 상수의 형태로 사용 가능하다.
          - 상수의 경우 접근제어자(private 또는 public)와 static 키워드를 함께 사용한다.
          - 실제 사용하면 private(public) static final 형태로 변수 선언한다.
          - static을 사용하면 컴파일 타임에 메모리 할당을 한번만 하기 때문에 효율성 측면에서 사용하는 것이 좋다.<br>
          
          2-2. 변수에 값을 할당하기 전에 별도의 로직이 실행되어야 하는 등 복잡할 때 __생성자에서 초기화__ 한다.
           ```java
          class Car {
                private final int position;
            
                Car() {
                    this.position = 0;
                }
            }
            ```
     3. 인자 자체에 final 키워드를 사용하여 __메소드 내부에서 인자를 변경할 수 없도록__ 할 수 있다.
     ```java
      void someMethod(final int number) {
        number = 3;
        // Cannot assign a value to final variable 'number'
      }
      ```
           
  2. final 메소드 : 해당 메소드는 오버라이드 될 수 없다.
  ```java
  class ParentClass {
      final void finalMethod() {
          // ...
      }
  }
  
  class ChildClass extends ParentClass {
      void finalMethod() {
          // 'finalMethod()' cannot override 'finalMethod()' in
          // 'ParentClass'; overridden method is final
      }
  }
  ```
  - 자식 클래스에서 final 키워드가 사용된 부모의 메소드는 재정의가 불가능하다. 
  - 즉, 오버라이딩 할 수 없다는 말이다. (컴퍼일 에러가 발생한다.)
  - 메소드에 final 키워드를 사용하는 경우는 코어 부분에서 변경을 원치 않는 메소드를 명시할 때 사용한다.
    
  3. final 클래스 : 해당 클래스는 상속할 수 없다.(부모클래스가 못됨)
  ```java
  final class FinalClass {
      // ...
  }
  
  class ChildClass extends FinalClass {
      // Cannot inherit from final 'null'
  }
  ```
  - 클래스에 final 키워드를 사용하면 상속할 수 없는 클래스가 된다.
  - 즉, 부모 클래스가 될 수 없다는 의미다.
  - 코드와 같이 상속을 시도하면 컴파일 에러가 난다.
    
> 참고 사이트 : https://hudi.blog/java-final/

### 2.enum 클래스
1. 개념
  ```java
  public enum Type {
      WALKING, RUNNING, TRACKING, HIKING
  }
  ```
  - 서로 관련있는 상수들을 모아 집합으로 정의한다.
  - enum 클래스 형을 기반으로 한 클래스형 선언을 한다.
2. 특징
  - 열거형으로 선언된 순서에 따라 0부터 index값을 가진다.(순차적으로 증가)
  - enum 열거형으로 지정된 상수들은 모두 대문자로 선언한다.
  - 열거형 변수들을 선언한 후 마지막에 세미콜론(;)을 찍지 않는다.
  - 상수와 특정 값을 연결시킬 경우 마지막에 세미콜론(;)을 붙여줘야한다.
3. enum 클래스 메소드 사용
 1) values() : 열거된 모든 원소를 배열에 담아 순서대로 반환한다.
  ```java
  ...
  public static void main(String[] args) {
      for(Type type : Type.values()) {
          System.out.println(type);
      }
  }
  ```
  ```txt
  WALKING
  UNNING
  TRACKING
  HIKING
  ```
 2) ordinal() : 원소에 열거된 순서를 정수로 반환한다.
 ```java
  ...
  Type tp = Type.HIKING;       
  System.out.println(tp.ordinal());
  ```
  ```txt
  3
  ```
 3) valueOf() : 매개변수로 주어진 String과 열거형에서 일치하는 이름을 갖는 원소를 반환한다. (없으면 IllegalArgumentException 예외 발생)
```java
...
Type tp1 = Type.WALKING;
Type tp2 = Type.valueOf("WALKING");
 
System.out.println(tp1);
System.out.println(tp2);
```
```txt
WALKING
WALKING
```
> 값 가져오는 방법은<br>1. enum형 객체를 만들거나(Type.WALKING) <br>2. valueOf()메소드를 시용해서(Type.valueOf("WALKING") 가져온다.
4. 열거형 상수를 다른 값과 연결하기
```java
enum Type {

// 상수("연결할 문자")
  WALKING("워킹화"), RUNNING("러닝화")
, TRACKING("트래킹화"), HIKING("등산화");
 
final private String name;
...
}

public class Shoes {
    public static void main(String[] args) {
        for(Type type : Type.values()){
            System.out.println(type.getName());
        }
    }
}
```
```txt
워킹화
러닝화
트래킹화
등산화
```
4. 클래스의 static final 이용해서 열거형 선언하기
```java
class Type {
    static final String WALKING = "워킹화";
    static final String RUNNING = "러닝화";
    static final String TRACKING = "트래킹화";
    static final String HIKING = "등산화";
}
 
public class Shoes {
    public static void main(String[] args) {
        String w = Type.WALKING;
        System.out.println(w);
    }
}
```
```txt
워킹화
```
> 참고 사이트 : https://opentutorials.org/module/1226/8025

## 질문 및 답변
