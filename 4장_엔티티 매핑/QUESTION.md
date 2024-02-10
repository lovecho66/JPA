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

## 질문 및 답변
