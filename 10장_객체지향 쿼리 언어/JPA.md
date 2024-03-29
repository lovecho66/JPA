  # 10장_프록시와 연관관계 관리

<b>✨설명 전 Point 잡고 가기✨</b> 
1. JPA는 복잡한 검색조건을 사용해서 엔티티 객체를 조회할 수 있는 다양한 쿼리 기술을 지원한다.
2. 쿼리 기술
   * JPQL
   * Criteria
   * QueryDSI
3. JPQL은 가장 중요한 객체지향 쿼리 언어이고 Criteria나 QueryDSL은 결국 JPQL을 편리하게 사용하도록 도와주는 기술이다.
4. 이 장 마지막에는 객체지향 쿼리에 대한 심화 내용을 다룬다.

# 10.1 객체지향 쿼리 소개 
## 10.1.1 JPQL 소개
 - JPQL(Java Persistence Query Language)은 엔티티 객체를 조회하는 객체지향 쿼리다.
 - JPQL 특징으로
   - SQL 문법과 비슷하다.
   - ANSI 표준 SQL이 제공하는 기능을 유사하게 지원한다.
   - SQL을 추상화했기때문에 특정 데이터베이스에 의존하지 않는다.
      <details>
      <summary>JPQL은 SQL을 추상화한 것?</summary>
      
      <!-- summary 아래 한칸 공백 두어야함 -->
      - 추상화는 특정한 세부 사항을 숨기고 핵심 개념 또는 기능에만 집중할 수 있도록하는 것을 의미한다.
      - JPQL이 SQL을 추상화한다는 것은 JPQL이 데이터베이스의 상호 작용하는 방법을 추상화한다는 의미다.
      - 결국 개발자가 데이터베이스에 대해 구체적으로 알 필요없이도 더 높은 수준에서 작업을 할 수 있게 도와준다.
      - 즉, JPQL의 세부 사항을 숨기고 개발자가 보다 추상적인 수준에서 작업할 수 있도록 합니다. 
      </details>
   - 데이터베이스 방언만 변경하면 JPQL을 수정하지 않아도 자연스럽게 데이터베이스를 변경할 수 있다.
   - JPQL이 제공하는 표준화된 함수를 사용하면 선택한 방언에 따라 해당 데이터베이스에 맞춘 적절한 SQL 함수가 실행된다.
   - 엔티티 직접조회, 묵시적 조인, 다형성 지원 등 이러한 이유로 JPQL은 SQL보다 간결하다. 

![image.jpg1](./images/10.1_1.PNG) |![image.jpg2](./images/10.1_2.PNG)
|----|----|

- JPQL에서 Member은 테이블 이름이 아니라 엔티티 이름이다.
- m.username은 테이블 컬럼명이 아니라 엔티티 객체의 필드다.
```java
em.CreateQuery(jpql,Member.class).getResultList()
```
- em.CreateQuery메소드에 실행할 JPQL과 반환할 엔티티 클래스 타입인 Member.class를 넘겨주고 getResultList() 메소드를 실행하면 JPA는 JPQL을 SQL로 변환해서 데이터베이스를 조회한다. 그리고  조회한 결과로 Member 엔티티를 생성해서 반환한다.

![image.jpg1](./images/10.1_3.PNG) |![image.jpg2](./images/10.1_4.PNG)
|----|----|

- JPQL을 실행하면 실제 SQL은 오른쪽과 같이 실행된다. 

## 10.1.2 Criteria 쿼리 소개
- Criteria는 JPQL을 생성하는 빌더 클래스다. 
  <details>

    <summary>빌더 패턴</summary>
    
    <!-- summary 아래 한칸 공백 두어야함 -->
    - 빌더 패턴은 객체를 생성하는 디자인 패턴 중 하나다.
    - 빌더 패턴은 객체의 생성과정을 단순화하고 유연하게 만든다.
    - 복잡한 객체를 생성할 때 사용되는 매개변수를 지정하거나 생햑할 수 있다. 
    객체의 생성 과정이 단계별로 나누어져있어 가독성 향상이되며 유지보수가 용이해진다. 
    - Critria와 QueryDSL에서 빌더 클래스라고 불리는 것은 이러한 빌더 패턴의 특징을 반영하기 위함이다.
    - 쿼리를 생성하는 과정을 객체의 생성과 유사하게 보기 때문에, 이를 표현하기 위해 "빌더"라는 용어를 사용한다.
      
  </details>
  query.select(m).where(...)
- Criteria의 장점은 문자가 아닌 프로그래밍 코드로 JQPL을 작성할 수 있다.
- Criteria는 문자가 아닌 코드로 JQPL을 작성해서 컴파일 시점에 오류를 발견할 수 있다. (컴파일시 오류 발견)
  (비교 : 문자기반 쿼리는 컴파일은 성공해서 서버에 배포했는데 해당 쿼리가 실행되는 런타임 시점에 오류가 발생한다는 단점이 있다.)
- Criteria의 장점은 아래와 같다. 
  - 컴파일 시점에 오류를 발견할 수 있다.
  - IDE 사용하면 코드 자동완성을 지원한다.
  - 동적 쿼리를 작성하기 편하다
  - JPA는 2.0부터 Criteria를 지원한다.  
![image.jpg1](./images/10.1_5.PNG)
- 쿼리가 아닌 코드로 작성한 것을 볼 수 있다. (.where(~))
- Criteria가 가진 장점이 많지만 모든 장점을 상쇄할 정도로 복잡하고 장황하다.
- Criteria로 작성한 코드도 한눈에 들어오지 않는다는 단점이 있다.

## 10.1.3 QueryDSL 소개
- QueryDSL JPQL 빌더 역할을 한다.
- QueryDSL의 장점은 코드 기반이면서 단순하고 사용하기 쉽다.
> 참고로, QueryDSL은 JPA 표준은 아니고 오픈소스 프로젝트다.<br>
  JPA,JDO,몽고DB,Java Collection, Lucene, Hibernate Search도 거의 같은 문법으로 지원한다. 
  Criteria보다 QueryDSL을 선호한다.

![image.jpg1](./images/10.1_6.PNG)

- QueryDSL도 어노테이션 프로세서를 사용해서 쿼리 전용 클래스를 만들어야한다.
- 이 소스에서 QueryDSL 쿼리 전용 클래스는 QMember다.
- QMember는 Member 엔티티 클래스를 기반으로 생성했다.

## 10.1.4 네이티브 SQL 소개
- JPA는 SQL을 직접 사용할 수 있는 기능을 지원하는데 이를 네이티브 SQL라 한다. 
- 특정 데이터베이스에 의존하는 기능을 사용해야할 때 네이티브 SQL을 사용한다. 
  - 오라클 데이터베이스만 사용하는 CONNECT BY 기능
  - 특정 데이터베이스에서만 동작하는 SQL 힌트
  - 위 기능들은 전혀 표준화되어 있지 않으므로 JPQL에서 사용할 수 없다.
- SQL은 지원하지만 JPQL이 지원하지 않는 기능도 있는데 이때도 네이티브 SQL을 사용한다.
- 네이티브 SQL의 단점은 특정 데이터베이스에 의존하는 SQL을 작성해야 한다는 점이다. 

![image.jpg1](./images/10.1_7.PNG)

- 네이티브 SQL은 em.createNativeQuery()를 사용해서 직접 작성한 sql을 데이터베이스에 전달한다.

## 10.1.5 JDBC 직접 사용, 마이바티스 같은 SQL 매퍼 프레임워크 사용
- JPA는 JDBC 커넥션을 획득하는 API를 제공하지 않는다.
- JDBC 커넥션에 직접 접근하고 싶으면 JPA 구현체가 제공하는 방법을 사용해야 한다.
![image.jpg1](./images/10.1_8.PNG)

- JDBC Connection을 획득하는 방법
  - JPA EntityManager에서 하이버네이트 Session을 구하고 Session의 doWork() 메소드를 호출한다.
- JDBC나 마이바티스를 JPA와 함께 사용하면 영속성 컨텍스트를 적절한 시점에 강제로 프러시해야 한다.
  - JDBC를 직접 사용하거나 마이바티스 같은 SQL 매퍼와 사용하면 모두 JPA를 우회해서 데이터베이스에 접근한다. 이렇게 우회하는 SQL에 대해서는 JPA가 전혀 인식을 하지 못해서 문제가 된다. (무결성 훼손)
- 다시 강조하면, JPA를 우회해서 SQL을 실행하기 직전에 영속성 컨텍스트를 수동으로 플러시해서 데이터베이스와 영속성 컨텍스트를 동기화하면 된다.
- 참고로,스프링 프레임워크를 사용하면 JPA와 마이바티스를 손쉽게 통합할 수 있다.
- 스프링 프레임워크의 AOP를 적절히 활용해서 JPA를 우회하여 데이터베이스에 접근하는 메소드를 호출할 때마다 영속성 컨텍스트를 플러시하면 된다.
  <details>

    <summary>스프링 프레임워크 AOP 사용해서 플러시</summary>
    
    <!-- summary 아래 한칸 공백 두어야함 -->

    ```java 
    @Aspect
    @Component
    public class FlushPersistenceContextAspect {
    
        @PersistenceContext
        private EntityManager entityManager;
    
        @Before("execution(* com.example.service.*.*(..)) && !@annotation(Transactional)")
        public void beforeNonTransactionalServiceMethods() {
            // 트랜잭션 어노테이션이 없는 서비스 메소드가 호출될 때마다 영속성 컨텍스트를 플러시
            entityManager.flush();
        }
    }

    ```
    - AOP는 스프링 프레임워크에서 제공하는 기능 중 하나다.
    - AOP의 핵심 개념은 Advice(조언),Join Point(결합 지점), Pointcut(결합 지점 선정) , Aspect(관점)등이 있다.
    - AOP를 활용해서 영속성 컨텍스트를 플러시 하기위해 Aspect를 사용한다.
    - Aspect(관점)은 여러 결합 지점과 Advice의 조합을 의미한다. 예를 들어, 로깅과 트랜잭션 관리를 위한 Aspect를 정의할 수 있다.
    - FlushPersistenceContextAspect 클래스는 @Aspect 어노테이션을 사용하여 Aspect로 정의한다.
    - @Before 어노테이션을 사용하여 Advice를 정의한다.
    - Aspect는 execution(* com.example.service.*.*(..)) && !@annotation(Transactional) 포인트컷 표현식을 사용하여 com.example.service 패키지에 속한 모든 메소드 중 @Transactional 어노테이션이 없는 메소드를 대상으로 한다.
    - Advice 내에서 entityManager.flush()를 호출하여 영속성 컨텍스트를 플러시한다.
    - 이렇게 하면 JPA가 관리하는 엔티티의 상태가 데이터베이스와 동기화된다.
  </details>
  
# 10.2 JPQL

![image.jpg1](./images/10.2_1.PNG) |![image.jpg2](./images/10.2_2.PNG)
|----|----|

- 예제 사용할 도메인 모델은 위와 같다.
- 왼쪽이 샘플 모델 UML이고 오른쪽이 샘플 모델 ERD다.
- 회원이 상품을 주문하는 다대다 관계라는 것을 특히 주의해서 보자.
- Address는 임베디드 타입인데 이것은 값 타입으로 uml에서 스테레오 타입을 사용해서 <<value>>로 정의했다. 

## 10.2.1 기본 문법과 쿼리 API
- JPQL도 SQL과 비슷하게 SELECT,UPDATE,DELETE문을 사용할 수 있다. (em.persist()메소드 사용하면 되므로 INSERT는 따로 없다.)
  ### SELECT
  ```java
  SELECT m FROM Member AS m where m.username = 'Hello'
  ```
  - 대소문자 구분
    - 엔티티와 속성은 대소문자를 구분한다. (Member 엔티티, username 속성)
    - SELECT,FROM,AS 같은 JPQL 키워드는 대소문자를 구분하지 않는다. 
  - 엔티티 이름
    - JPQL에서 사용한 Member는 클래스 명이 아니라 엔티티명이다.
    - 엔티티 명은 @Entity(name="xxx")로 지정할 수 있다.
    - 기본값인 클래스 명을 엔티티명으로 사용하는 것을 추천한다.
  - 별칭은 필수
    ![image.jpg1](./images/10.2_3.PNG)
    - Member AS m 처럼 Member 에 m이라는 별칭을 줬다.
    - JPQL은 별칭을 필수로 사용해야 한다. (오류발생)
    - AS는 생략 가능하다.
  > 하이버네이트는 JPQL 표준도 지원하지만 더 많은 기능을 가진 HQL(Hibernate Query Language)를 제공한다.
    JPA 구현체로 하이버네이트를 사용하면 HQL도 사용할 수 있다.
    HQL 사용 : SELECT username FROM Member (username 처럼 별칭 없이 사용가능)
  
  > JPA 표준 명세는 별칭을 식별 변수라는 용어로 정의함

  ### TypeQuery,Query
  - 작성한 JPQL을 실행하려면 쿼리 객체를 만들어야 한다.
  - 쿼리 객체는 TypeQuery와 Query가 있다.
    - TypeQuery 선택 : 반환할 타입을 명확하게 지정
    - Query 선택 : 반환 타입을 명확하게 지정할 수 없음

  ![image.jpg1](./images/10.2_4.PNG)

  - em.createQuery()에 반환타입 Member.class 지정한다.
  - TypeQuery를 반환한다.

  ![image.jpg1](./images/10.2_5.PNG)
  ```java
  SELECT m.username from Member m;  
  ```
  - SELECT절에 여러 엔티티나 컬럼을 선택할 때는 반환할 타입이 명확하지 않으므로 Query객체를 사용해야한다.
  ```java
  SELECT m.username,m.age from Member m;
  ```
  - Query객체는 SELECT 절의 조회 대상이 하나면 Object 반환하고 둘 이상이면 Object[]를 반환한다.
  ### 결과 조회
  - 다음 메소드들을 호출하면 실제 쿼리를 실행해서 데이터베이스를 조회한다.
    - query.getResultList() : 결과를 예제로 반환한다. 만약 결과과 없으면 빈 컬렉션을 반환한다.
    - query.getSingleResult() : 결과가 정확히 하나일 때 사용한다. (정확히 1개가 아니면 예외발생)
      - 결과가 없으면 javax.persistence.NoResultException예외가 발생한다.
      - 결과가 1개보다 많으면 javax.persistence.NonUniqueResultException 예외가 발생한다.

## 10.2.2 파라미터 바인딩
  - JDBC는 위치 기준 파라미터 바인딩만 지원하지만 JPQL은 이름 기준 파라미터 바인딩도 지원한다.

    - 이름 기준 파라미터
    ![image.jpg1](./images/10.2_6.PNG)

      - 이름 기준 파라미터는 파라미터 이름으로 구분하는 방법으로 앞에 : 를 사용한다.
      - JPQL을 보면 :username이라는 이름 기준 파라미터를 정의하고 query.setParameter()에서 username이라는 이름으로 파라미터를 바인딩한다.
      ```java
      List<Member> members = em.createQuery("SELECT m FROM Member m where m.username = :username", Member.class)
      .setParameter ("username", usernameParam).getResultList() ;
      ```
      - JPQL API는 대부분 메소드 체인 방식으로 설계되어 있어서 연속해서 작성할 수 있다.

    - 위치 기준 파라미터
    ![image.jpg1](./images/10.2_7.PNG)
      - 위치 기준 파라미터를 사용하려면 ? 다음에 위치 값을 주면 된다.
      - 위치 기준 파라미터 방식보다는 이름 기준 파라미터 바인딩 방식을 사용하는 것이 더 명확하다.
  > 파라미터 바인딩 방식을 사용해야하는 이유 <br>
    1) 보안<br>
    JPQL을 수정해서 다음 코드처럼 파라미터 바인딩 방식을 사용하지 않고 직접 문자를 더해 만들어 넣으면 악의적인<br>
    사용자에 의해 SQL 인젝션 공격을 당할 수 있다.<br>
    (String username = "user123";<br>
    String jpqlQuery = "SELECT u FROM User u WHERE u.username = '" + username + "'";)<br>
    2) 성능<br>
    파라미터의 값이 달라도 같은 쿼리로 인식해서 JPA는 JPQL을 SQL로 파싱한 결과를 재사용할 수 있다.<br>
    데이터베이스도 내부에서 실행한 SQL을 파싱해서 사용하는데 같은 쿼리는 파싱한 결과를 재사용할 수 있다.<br>
    (애플리케이션과 데이터베이스 모두 해당 쿼리의 파싱 결과를 재사용할 수 있어서 전체 성능이 향상된다.)<br>
  파라미터 바인딩 방식은 선택이 아닌 필수다.

## 10.2.3 프로젝션
- SELECT 절에 조회할 대상을 지정하는 것을 프로젝션이라한다.
- SELECT {프로젝션 대상} FROM으로 대상을 선택한다.
- 프로젝션 대상은 아래 3가지가 있다. 
  - 엔티티
  - 임베디드 타입
  - 스칼라 타입 (숫자,문자등 기본 데이터 타입)
### 엔티티 프로젝션
![image.jpg1](./images/10.2_8.PNG)

- 위 JPQL 둘 다 엔티티를 프로젝션 대상으로 사용했다.
- 엔티티를 프로젝션은 객체를 바로 조회하는 것이다.
- __조회한 엔티티는 영속성 컨텍스트에서 관리된다.__

### 임베디드 타입 프로젝션

- 임베디드 타입은 조회의 시작점이 될 수 없다는 제약이 있다. 
- 샘플 모델 Address는 UML에서 스테레오 타입을 사용해서 <<Value>>로 정의했다.
  ERD를 보면 ORDERS 테이블에 포함되어 있다.
    <details>

    <summary>스테레오 타입</summary>
    
    <!-- summary 아래 한칸 공백 두어야함 -->
    UML에서 스테레오 타입(Stereotype)은 일반적인 모델 요소에 대한 새로운 의미나 속성을 추가하는 데 사용됩니다.        <<VALUE>>는 UML에서 정의된 스테레오 타입 중 하나다.
    </details>
  
  ```java
  String query = "SELECT a FROM Address a";
  ```
  - 임베디드 타입은 조회의 시작점이 될 수 없다는 제약이 있다.
  - 위 쿼리처럼 임베디드 타입인 Address를 조회의 시작점으로 사용해서 잘못된 쿼리다.

  ```java
  String query = "SELECT o.address FROM Order o";
  List<Address> addresses = em.createQuery(query, Address.class).getResultList();
  ```
  - Order 엔티티가 시작점이다. 엔티티를 통해서 임베디드 타입을 조회할 수 있다.

  ```xml
  select order.city,
         order.street,
         order.zipcode
  from
    Orders order
  ```
  - 위와 같은 쿼리가 실행된다.
  - 임베디드 타입은 엔티티 타입이 아닌 값 타입이다.
  - 따라서 이렇게 __직접 조회한 임베디드 타입은 영속성 컨텍스트에서 관리 되지 않는다.__

### 스칼라 타입 프로젝션
```java
List<String> usernames =
em.createQuery("SELECT username FROM Member m", String.class).getResultList();
```
- 숫자,문자,날짜와 같은 기본 데이터 타입들을 스칼라 타입이라 한다. 

### 여러 값 조회
![image.jpg1](./images/10.2_9.PNG)
- 엔티티를 대상으로 조회하면 편하겠지만 꼭 필요한 데이터들만 선택해서 조회해야 할 때도 있다.
- 프로젝션에 여러 값을 선택하면 TypeQuery는 사용할 수 없고 대신에 Query를 사용해야한다.

![image.jpg1](./images/10.2_10.PNG)
- 제너릭에 Object[]를 사용하면 위 코드처럼 조금 더 간결하게 개발할 수 있다.

![image.jpg1](./images/10.2_11.PNG)
- 엔티티 타입도 여러 값을 함께 조회할 수 있다.

### New 
![image.jpg1](./images/10.2_12.PNG) |![image.jpg2](./images/10.2_13.PNG)
|----|----|
- username과 age 두 필드를 프로젝션해서 타입을 지정할 수 없어서 TypeQuery를 사용할 수 없다. 그래서 Object[]를 반환받받았다.
- 개발시 Object[]를 직접 사용하지 않고 UserDTO처럼 의미 있는 객체로 변환해서 사용할거다.
- 객체 변환 작업은 지루하다. NEW 명령어를 사용하자!

![image.jpg1](./images/10.2_14.PNG)
- SELECT 다음에 NEW 명령어를 사용하면 반환받을 클래스를 지정할 수 있다.
- 클래스으이 생성자에 JPQL 조회 결과를 넘겨줄 수 있다.
- NEW 명령어 사용한 클래스로 TypeQuery 사용할 수 있어서 지루한 객체 변환 작업을 줄 일 수 있다.
- NEW 명령어 사용시 주의해야할 점 2가지
  - 패키지 명을 포함한 전체 클래스 명을 입력해야한다.
    EX> jpabook.jpql.~
  - 순서와 타입이 일치하는 생서자가 필요하다. 
  
## 10.2.4 페이징 API 
![image.jpg1](./images/10.2_15.PNG)
- 데이터베이스마다 페이징을 처리하는 SQL 문법이 다르다.
- JPA는 페이징을 두 API로 추상화했다.
  - setFirstResult(int startPosition) : 조회 시작 위치(0부터 시작한다.)
  - setMaxResult(int maxResult) : 조회할 데이터 수
- FirstResult 시작은 10이므로 11번째부터 시작해서 20건의 데이터를 조회한다. (11~30번)
  
![image.jpg1](./images/10.2_16.PNG) | ![image.jpg2](./images/10.2_17.PNG)
|----|----|

```XML
//제 10,22 오라클(org.hit>ernate.dialect.Oracte10gDiatect)
SELECT *
FROM
( SELECT ROW_.*, ROWNUM ROWNUM_
FROM ( SELECT M.ID AS ID,
              M.AGE AS AGE,
              M.TEAM_ID AS TEAM_ID,
              M.NAME AS NAME
              FROM MEMBER M
              ORDER BY M.NAME
      ) ROW_
WHERE ROWNUM <= ?
)
WHERE ROWNUM > ?
```
```XML
//SQLServer(org.hibernate.dialect.SQLServer2008Dialect)
WITH query AS (
  SELECT
  inner_query.
  ROW_NUMBER() OVER (ORDER BY CURRENT_TIMESTAMP) as
  _ hibernate_row_nr_
FROM
    ( select
          TOP (?) m.id as id,
          m.age as age,
          m.team_id as team_id,
          m.name as name
    from Member m
    order by m.name DESC
    ) inner_query
)
SELECT id, age, team_id, name
FROM query
WHERE hibernate row nr >= ? AND hibernate row nr < ?
```
- 데이터베이스마다 다른 페이징 처리를 같은 API로 처리할 수 있는 것은 데이터베이스 방언덕분이다. (JPA가 제공)
- 페이징 SQL을 더 최적화하고 싶다면 JPA가 제공하는 페이징 API가 아닌 네이티브 SQL을 직접 사용해야한다.
  
## 10.2.5 집합과 정렬
```XML
select  COUNT (m),    //회원수 
        SUM(m.age) ， // 나이합 
        AVG(m.age),   //평균나이 
        MAX (m.age),  //최대나이 
        MIN (m.age)   //최소나이 
from Member m
```
- 집합은 집합함수와 함께 통계 정보를 구할 때 사용한다.

### 집합 함수
![image.jpg1](./images/10.2_18.PNG)

- 집합 함수 사용시 참고사항
  - NULL값은 무시하므로 통계에 잡히지 않는다.(DISTINCT가 정의되어 있어도 무시)
  - 만약 값이 없는데 SUM,AVG,MAX,MIN 함수를 사용하면 NULL값이 된다.
  - 단,COUNT는 값이 없으면 0이 된다.
  ```XML
  SELECT COUNT(DISTINCT m.age) FROM Member m
  ```
  - DISTINCT를 집합 함수 안에 사용해서 중복된 값을 제거하고 나서 집합을 구할 수 있다. 
  - DISTINCT를 COUNT에서 사용할 때 임베디드 타입은 지원하지 않는다.

### GROUP BY, HAVING
![image.jpg1](./images/10.2_19.PNG) |![image.jpg2](./images/10.2_20.PNG)
|----|----|

- GROUP BY 와 HAVING등 이런 쿼리들을 보통 리포팅 쿼리나 통계쿼리라 한다. 
- 통계 쿼리를 잘 활용하면 애플리케이션으로 수십 라인을 작성할 코드도 단 몇 줄이면 처리할 수 있지만
  통계 쿼리는 보통 전체 데이터를 기준으로 처리하므로 실시간으로 사용하기엔 부담이 많다.
- 결과가 아주 많다면 통계 결과만 저장하는 테이블을 별도로 만들어 두고 사용자가 적은 새벽에 통계 쿼리를 실행해서 그 결과를 보관하는 것이 좋다. 

### 정렬 ORDER BY

## 10.2.6 JPQL 조인
### 내부 조인
![image.jpg1](./images/10.2_21.PNG)
- 내부조인은 INNER JOIN을 사용한다.
  
![image.jpg1](./images/10.2_22.PNG) |![image.jpg2](./images/10.2_23.PNG)
|----|----|

- JPQL 내부 조인 구문과 SQL의 조인은 약간 차이가 있다. (SQL과 똑같이 쓰면 문법 오류 발생)
- JPQL 조인의 특징은 연관 필드를 사용하는건데 여기서 m.team이 연관필드로 다른 엔티티와 연관관계를 가질 수 있게 해준다.
- 연관 필드는 다른 엔티티와 연관관계를 가지기 위해 사용하는 필드다.

```java
SELECT m.username, t.name
FROM Member m JOIN m.team t
WHERE t.name = '팀A'
ORDER BY m.age DESC
```
- 위 쿼리는 팀A 소속인 회원을 나이 내림차순으로 정렬하고 회원명과 팀명을 조회한다.
![image.jpg1](./images/10.2_24.PNG) |![image.jpg2](./images/10.2_25.PNG)
|----|----|

- 서로 다른 타입의 두 엔티티를 조회했으므로 TypeQuery를 사용할 수 없다.
- 그래서 Object[]사용해서 조회한다.

### 외부 조인

![image.jpg1](./images/10.2_26.PNG) |![image.jpg2](./images/10.2_27.PNG)
|----|----|
- 외부조인은 기능상 SQL 외부 조인과 같다.

### 컬렉션 조인
- 일대다 관계나 다대다 관계처럼 컬렉션을 사용하는 곳에 조인하는 것을 컬렉션 조인이라 한다.
- [회원 -> 팀] 다대일 조인이면서 __단일 값 연관 필드(m.team)을 사용한다.__
- [팀 -> 회원] 일대다 조인이면서 __컬렉션 값 연관 필드(m.members)를 사용한다.__
```java
SELECT t,m FROM Team t LEFT t.members m // Team -> members
```
-컬렉션 값 연관 필드로 외부 조인을 했다.

### 세타 조인
![image.jpg1](./images/10.2_28.PNG)
- WHERE 절을 사용해서 세타 조인을 할 수 있다.
- 세타 조인을 사용하면 외래키로 서로 연결되지 않아도 조인 가능하다.

### JOIN ON 절(JPA 2.1)
![image.jpg1](./images/10.2_29.PNG)
 - JPA 2.1부터 조인할 때 ON절 지원한다.
 - ON절을 사용하면 조인 대상을 필터링하고 조인할 수 있다. 
 - SQL 결과를 보면 and t .name= 'A'로 조인 시점에 조인 대상을 필터링한다.

## 10.2.7 패치 조인
- 페치조인은 SQL에서 이야기하는 조인의 종류는 아니고 JPQL에서 성능 최적화를 위해 제공하는 기능이다.
- 연관된 엔티티나 컬렉션을 한번에 같이 조회하는 기능이다.
- join fetch 명령어로 사용할 수 있다. 

### 엔티티 페치 조인 
```java
//JPQL
SELECT m FROM Member m join fetch m.team;
```
- 연관된 엔티티나 컬렉션을 함께 조회하는데 여기서 회원(m)과 팀(m.team)을 함께 조회한다. 
- 일반적인 JPQL 조인과는 m.team 다음에 별칭이 없는데 페치 조인은 별칭을 사용할 수 없다.
  (하이버네이트는 페치 조인에도 별칭을 허용한다.)
  
![image.jpg1](./images/10.2_30.PNG) |![image.jpg2](./images/10.2_31.PNG)
|----|----|  

- 엔티티 페치 조인 JPQL에서 SELECT m으로 회원 엔티티만 선택했는데 실행된 SQL을 보면 SELECT M.*,T.*로 회원과 연관된 팀도 함께 조회된 것을 확인할 수 있다.

![image.jpg1](./images/10.2_32.PNG)
- 회원을 조회할 때 페치 조인을 사용해서 팀도 함께 조회했으므로 연관된 팀 엔티티는 프록시가 아닌 실제 엔티티다.
- 따라서 연관된 팀을 사용해도 지연 로딩이 일어나지 않는다.
- 프록시가 아닌 실제 엔티티이므로 회원 엔티티가 영속성 컨텍스트에서 분리되어 준영속 상태가 되어도 연관된 팀을 조회할 수 있다. 

### 컬렉션 페치 조인 

![image.jpg1](./images/10.2_33.PNG) |![image.jpg2](./images/10.2_34.PNG)
|----|----|  
- 팀(t)을 조회하면서 페치 조인을 사용해서 연관된 회원 컬렉션도 함께 조회한다.

![image.jpg1](./images/10.2_35.PNG) |![image.jpg2](./images/10.2_36.PNG)
|----|----|  
- 컬렉션 페치 조인한 JPQL에서 SELECT t로 팀만 선택했는데 실행된 SQL보면 T.*,M.*로 팀과 연관된 회원도 함께 조회한 것을 확인할 수 있다.
- TEAM 테이블에서 팀A는 하나지만 MEMBER 테이블과 조인하면서 결과가 증가해서 팀A가 2건 조회되었다.
> 일대다 조인은 결과가 증가할 수 있지만 일대일,다대일 조인은 결과가 증가하지 않는다.

![image.jpg1](./images/10.2_37.PNG)
- 지연 로딩이 발생하지 않는 이유는 페치 조인(fetch join)을 사용했기 때문이다.
- 페치 조인을 사용하면 연관된 엔티티들을 즉시 로딩하는 것이기 때문에 추가적인 쿼리 실행이 발생하지 않는다.
- 일반적인 상황에서는 연관된 엔티티를 지연 로딩으로 가져오는데, 이는 필요할 때에만 데이터베이스에서 실제 데이터를 가져오도록 한다.
- 그러나 페치 조인을 사용하면 연관된 엔티티를 함께 로드하여 지연 로딩을 피할 수 있습니다.

<details>

    <summary>페치 조인 더 보기</summary>
    
    <!-- summary 아래 한칸 공백 두어야함 -->
    페치 조인을 사용하면 한 번의 쿼리로 필요한 모든 연관된 엔티티를 함께 로드할 수 있기 때문에 <br>
    n+1 문제가 발생하지 않는다. 따라서 페치 조인은 n+1 문제를 피하고 성능을 향상시키는 데 매우 유용하다.<br>
    따라서 다수의 연관된 엔티티를 함께 가져와야 하는 상황에서는 페치 조인(fetch join)을 고려하는 것이 좋다.<br>
    페치 조인을 사용하면 추가적인 쿼리를 보내지 않아도 연관된 엔티티를 함께 로드할 수 있으므로 성능이 향상된다.<br>
    그러나 페치 조인은 데이터베이스에서 많은 양의 데이터를 가져오므로 주의해야한다.
</details>
 
### 페치조인과 DISTINCT
- JPQL의 DISTINCT 명령어는 아래 2가지 작업을 해준다. 
  - SQL에서의 DISTINCT를 추가하는 것
  - 애플리케이션에서 한번 더 중복을 제거하는 것
  - 
![image.jpg1](./images/10.2_38.PNG) |![image.jpg2](./images/10.2_39.PNG)
|----|----|    
- 데이터가 달라서 중복제거해도 달라지는게 없다.
- 애플리케이션에서 엔티티의 중복을 제거한다. 그러면 팀A의 객체는 하나만 남는다. 

### 페치조인과 일반 조인의 차이

![image.jpg1](./images/10.2_40.PNG)
- JPQL은 결과를 반환할 때 연관관계까지 고려하지 않는다. 단지 SELECT 절에 지정한 엔티티만 조회할 뿐이다.
- 그래서 조인만한 회원도 무조건 함께 조회될거라고 기대해서는 안된다.
  
![image.jpg1](./images/10.2_41.PNG)
- 만약 회원 컬렉션을 지연 로딩을 설정하면 프록시나 아직 초기화하지 않은 컬렉션 래퍼를 반환한다.
- 즉시 로딩으로 설정하면 회원 컬렉션을 즉시 로딩하기 위해 쿼리를 한번 더 실행한다.

![image.jpg1](./images/10.2_42.PNG)
- 반면에 페치 조인을 사용하면 연관된 엔티티도 함께 조회한다.

### 페치조인 특징과 한계

#### 페치조인의 특징
- 페치조인을 사용하면 SQL 한번으로 연관된엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여 성능을 최적화할 수 있다.
- 엔티티에 직접 적용하는 로딩 전략은 애플리케이션 전체에 영향을 미치므로 글로벌 로딩 전략이라고 한다.

<details>
    <summary>글로벌 로딩 전략</summary>
    
    <!-- summary 아래 한칸 공백 두어야함 -->
    
    글로벌 로딩 전략은 주로 persistence.xml 파일이나 JPA 속성 설정을 통해 설정된다.
    예를 들어, persistence.xml 파일에서 default-lazy-loading 또는 default-fetch 속성을 사용하여 전체적인 로딩 전략을 설정할 수 있다.
</details>
- 페치 조인은 글로벌 로딩 전략보다 우선한다.
  EX) 글로벌 로딩 전략을 지연 로딩으로 설정해도 JPQL에서 페치 조인을 사용하면 페치 조인을 적용해서 함께 조회한다. 
- 최적화를 위해 글로벌 로딩 전략을 즉시 로딩으로 설정하면 애플리케이션 전체에서 항상 즉시 로딩이 일어난다. <br>
  일부는 빠를 수 있지만 전체로 보면 사용하지 않는 엔티티를 자주 로딩하므로 오히려 성능에 악영향을 미칠 수 있다. <br>
  
  __글로벌 로딩 전략은 될 수 있으면 지연 로딩을 사용하고 최적화가 필요하면 페치 조인을 적용하는 것이 효과적이다.__

- 페치 조인을 사용하면 연관된 엔티티를 쿼리 시점에 조회하므로 지연 로딩이 발생하지 않는다.
- 따라서 준영속 상태에서도 객체 그래프를 탐색할 수 있다.

#### 페치조인의 한계
- 폐치 조인 대상에는 별칭을 줄 수 없다.
  - SELECT,WHERE절,서브 쿼리에 페치 조인 대상을 사용할 수 없다.
  - JPA 표준에는 지원하지 않지만 하이버네이트를 포함한 몇몇 구현체들은 페치 조인에 별칭을 지원한다.
  - 하지만 별칭을 잘못 사용하면 연관된 데이터 수가 달라져서 데이터 무결성이 깨질 수 있으므로 조심해서 사용해야한다.
  - 특히 2차 캐시와 함께 사용할 때 조심해야하는데 연관된 데이터 수가 달라진 상태에서 2차 캐시에 저장되면 다른 곳에서 조회할 때도 연관된 데이터 수가 달라지는 문제가 발생할 수 있다.
- 둘 이상의 컬렉션을 페치할 수 없다.
  - 컬렉션 * 컬렉션의 카테시안 곱이 만들어지므로 주의해야한다. (예외 발생)
- 컬렉션을 페치 조인하면 페이징API를 사용할 수 없다.
  - 컬렉션(일대다)가 아닌 단일 값 연관 필드(일대일,다대일)들은 페치 조인을 사용해도 페이징 API를 사용할 수 있다.
  - 하이버네이트에서 컬렉션을 페치 조인하고 페이징 API를 사용하면 경고 로그를 남기면서 메모리에서 페이징 처리를 한다.
  - 데이터가 적으면 상관없지만 데이터가 많으면 성능 이슈와 메모리 초과 예외가 발생할 수 있어서 위험하다.

- 페치조인은 SQL 한번으로 연관된 여러 엔티티를 조회할 수 있어서 성능 최적화에 상당히 유용하다. 실무에서 자주 사용
- 모든 것을 페치 조인으로 해결할 수 없다. 페치 조인은 객체 그래프를 유지할 때 사용하면 효과적이다.
- 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 한다면 억지로 페치 조인을 사용하기보다는 여러 테이블에서 필요한 필드만 조회해서 DTO로 반환하는 것이 더 효과적이다.
  
## 10.2.8 경로 표현식
### 경로 표현식
- 경로 표현식은 .(점)을 찍어 객체 그래프를 탐색하는 것이다.
- 경로 표현식 용어정리
  - 상태필드 : 단순히 값을 저장하기 위한 필드(필드 OR 프로퍼티)
  - 연관 필드 : 연관관계를 위한 필드, 임베디드 타입 포함(필드 OR 프로퍼티)
    - 단일 값 연관 필드 : @ManyToOne , @OneToOne 대상이 엔티티 
    - 컬렉션 값 연관 필드 : @OneToMany , @ManyToMany 대상이 컬렉션
  - 상태 필드는 단순히 값을 저장하는 필드고 연관 필드는 객체 사이의 연관관계를 맺기 위해 사용하는 필드다. 

![image.jpg1](./images/10.2_43.PNG)
- 상태 필드 : t.username, t.age
- 단일 값 연관 필드 : m.team (@ManyToOne)
- 컬렉션 값 연관 필드 : m.orders (@OneToMany)

### 경로 표현식과 특징
- 경로 표현식을 사용해서 경로 탐색을 하려면 다음 3가지 경로에 따라 어떤 특징이 있는지 이해해야한다.
  - 상태 필드 경로 : 경로 탐색의 끝이다. 더는 탐색할 수 없다.
  - 단일 값 연관 경로 : __묵시적으로 내부 조인이 일어난다. 단일 값 연관 경로는 계속 탐색할 수 있다.__
  - 컬렉션 값 연관 경로 : __묵시적으로 내부 조인이 일어난다. 더는 탐색할 수 없다. 단 FROM 절에서 조인을 통해 별칭을 얻으면 별칭으로 탐색할 수 있다.__

### 상태 필드 경로 탐색
```xml
--JPQL
select m.username,m.age from Member m

--SQL
select m.name,m.age from Member m
```

### 단일 값 연관 경로 탐색
```xml
--JPQL
select o.member from Order o

--SQL
select m.*
from Orders o
inner join Member m on o.member_id = m.id
```
- JPQL을 보면 o.member를 통해 주문에서 회원으로 단일 값 연관 필드로 경로 탐색을 했다.
- 이때 단일 값 연관 필드로 경로 탐색을 하면 SQL에서 내부 조인이 일어난다.
- 이런걸  묵시적 조인이라 한다.
- 묵시적 조인은 모두 내부 조인이다.
- 외부 조인은 명시적으로 JOIN 키워드를 사용해야 한다.
- 단일 값 연관 경로 탐색시 조인
  - 명시적 조인 : JOIN을 직접 적어주는 것
  - 묵시적 조인 : 경로 표현식에 의해 묵시적으로 조인이 일어나는 것 (내부 조인 INNER JOIN만 할 수 있다. )

### 컬렉션 값 연관 경로 탐색

![image.jpg1](./images/10.2_44.PNG) |![image.jpg2](./images/10.2_45.PNG)
|----|----|  
- 실행된 SQL을 보면 총 3번의 조인이 발생했다.
- o.address처럼 임베디드 타입에 접근하는 것도 단일 값 연관 경로 탐색이지만 주문 테이블에 이미 포함되어 있으므로 조인이 발생하지 않는다.

### 컬렉션 값 연관 경로 탐색
```java
// 성공 사례
select t.members from Team t;

// 실패 사례 - 컬렉션에서 경로 탐색 (별칭 사용x)
select t.members.username from Team t;

//컬렉션에서 경로 탐색(조인, 별칭 사용 o)
select m.username from Team t join t.member m
```
- t.members처럼 컬렉션까지는 경로 탐색이 가능하다.
- 하지만 t.mebmers.username처럼 컬렉션에서 경로 탐색을 시작하는 것은 안된다.
- 컬렉션에서 경로를 탐색하고 싶으면 join을 사용해서 새로운 별칭을 획득 후 별칭부터 다시 경로를 탐색해야한다. 

```java
select t.members.size from Team t
```
- 컬렉션은 컬렉션의 크기를 구할 수 있는 size라는 특별한 기능을 사용할 수 있다.
- size를 사용하면 COUNT 함수를 사용하는 SQL로 적절히 변환된다.

### 경로 탐색을 사용한 묵시적 조인 시 주의사항
- 경로 탐색을 사용하면 묵시적 조인이 발생해서 SQL에서 내부 조인이 일어날 수 있다.
- 주의사항
  - 항상 내부 조인이다.
  - 컬렉션은 경로 탐색의 끝이다.
    - 컬렉션에서 경로 탐색을 하려면 명시적으로 조인해서 별칭을 얻어야 한다.
  - 경로 탐색은 주로 SELECT, WHERE절(다른 곳에서도 사용됨)에서 사용하지만 묵시적 조인으로 인해 SQL의 FROM 절에 영향을 준다. 
  <details>
      <summary>묵시적 조인-FROM절 영향</summary>
      
      <!-- summary 아래 한칸 공백 두어야함 -->
      묵시적 조인은 일반적으로 JOIN 키워드를 사용하지 않고, WHERE 절에서 조인 조건을 명시하여 조인을 수행하는 방식이다.
      WHERE 절에서 조인 조건을 명시했지만, 명시적으로 FROM 절에 해당 테이블을 포함시키지 않았기 때문에 어떤걸 먼저 수행해야할지
  모른다. 
  </details>
   
  
## 10.2.9 서브쿼리
- JPQL도 SQL처럼 서브쿼리를 지원한다.
- JPQL 서브쿼리를 사용하는데는 몇가지 제약이 있다.
  - 서브쿼리를 WHERE,HAVING절에서만 사용할 수 있다.
  - SELECT, FROM 절에서는 사용할 수 없다.
  > 하이버네이트의 HQL은 SELECT 절의 서브쿼리도 허용한다. 하지만 아직까지 FROM 절의 서브쿼리는 지원하지 않는다.
    일부 JPA 구현체는 FROM절의 서브 쿼리도 지원한다.
- 서브 쿼리 함수
  - EXISTS (서브쿼리)
  - ALL/ANY/SOME (서브쿼리) : ALL 조건 모두 만족, ANY OR SOME : 조건을 하나라도 만족
  - IN (서브쿼리)  : 서브쿼리 결과중 하나라도 같은 것이 있으면 참
## 10.2.10 조건식
![image.jpg1](./images/10.2_46.PNG)

- 연산자 우선 순위
  1. 경로 탐색 연산(.)
  2. 수학연산: +,-(단항 연산자),*,/,+,-
  3. 비교연산: = > >= < <= , <> , BETWEEN, LIKE, IN,EMPTY, EXISTS ,MEMBER [OF] - 부정 포함
  4. 논리 연산 : NOT, AND ,OR

- 논리 연산과 비교식
  - 논리 연산
  - AND： 둘 다 만족하면 참
  - OR: 둘 중 하나만 만족해도 참 
  - NOT： 조건식의 결과 반대

  -  비교식
    비교식은 다음과 같다.
    = | > | >= | < | <= | <>

- Between, IN, Like, NULL 비
  - Between 식
    - BETWEEN A AND B
    - X는 A ~ B 사이의 값이면 참(A, B 값 포함)
  - IN 식
    - 표와 같은 값이 예제에 하나라도 있으면 참아다. W 식의 예제에는 서브쿼리를 사용할 수 있다.
  - Like 식
    - 문자표현식과 패턴값을 비교한다.
      - %(퍼센트): 아무 값들이 입력되어도 된다(값이 없어도 됨).
      - _(언더라인): 한 글자는 아무 값이 입력되어도 되지만 값이 있어야 한다.
  - NULL 비교식
    -  NULL 인지 비교한다. NULL은 =으로 비교하면 안 되고 꼭 IS NULL을 사용한다.

- 컬렉션 식 
  - EMPTY
  
    ![image.jpg1](./images/10.2_47.PNG) |![image.jpg2](./images/10.2_48.PNG)
    |----|----|
    
      - 컬렉션은 컬렉션 식 이외에 다른 식은 사용할 수 없다.
      - 문법 : 컬렉션 값 연관 경로 IS [NOT] EMPTY
      - 설명 : 컬렉션에 값이 비었으면 참
      - 컬렉션은 컬렉션 식만 사용할 수 있다는 점을 주의하다. IS NULL처럼 컬렉션 식이 아닌 것은 사용할 수 없다.
  - MEMBER 
    ![image.jpg1](./images/10.2_49.PNG)
      - 문법 : 엔티티나 값 [NOT] MEMBER [OF] 컬렉션 값 연관 경로
      - 설명 : 엔티티나 값이 컬렉션에 포함되어 있으면 참

- 스칼라 식
  - 스칼라는 숫자,문자,날짜,CASE,엔티티 타입 같은 가장 기본적인 타입들을 말한다.
  ![image.jpg1](./images/10.2_50.PNG)
  ![image.jpg1](./images/10.2_51.PNG)
  ![image.jpg1](./images/10.2_52.PNG)

```java
select CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP from Team t 
//결과 : 2013-08-19, 23:38:17, 2013-08-19 23:38:17.736
```
```java
종료 이벤트 조회
select e from Event e where e .endDate < CURRENT_DATE
```

```java
select year (CURRENT_TIMESTAMP) , month (CURRENT_TIMESTAMP) , 
day(CURRENT_TIMESTAMP) 
from Member
```
- 하이버네이트는 날짜 타입에서 년, 월，일, 시간，분, 초 값을 구하는 기능을 지원 
한다.(YEAR, MONTH, DAY, HOUR, MINUTE, SECOND)

- CASE 식
  - 특정 조건에 따라 분기할 때 CASE 식을 사용한다. CASE 식은 4가지 종류가 있다.
    ■ 기본 CASE
    ■ 심플 CASE
    ■ COALESCE
    ■ NULLIF

  ![image.jpg1](./images/10.2_53.PNG) |![image.jpg2](./images/10.2_54.PNG)| ![image.jpg1](./images/10.2_55.PNG)
  |----|----|----|
  
  ![image.jpg2](./images/10.2_56.PNG) | ![image.jpg1](./images/10.2_57.PNG) 
  |----|----|
  
## 10.2.11 다형성 쿼리
  ![image.jpg1](./images/10.2_58.PNG)
- JPQL로 부모 엔티티를 조회하면 그 자식 엔티티도 함께 조회한다
- Item의 자식으로 Book, Album, Movie가 있다.

```java
List resultList = em.createQuery ("select i from Item i").getResultList()
```
  - 단일 테이블 전략 (InheritanceType.SINGLE_TABLE)
    //SQL
    SELECT * FROM ITEM
    
  - 조인 전략(InheritanceType. JOINED)
    // SQL
    SELECT i.ITEM_ID, i.DTYPE, i.name, i.price, i.stockQuantity, 
          b.author, b.isbn, 
          a.artist, a.etc, 
          m,actor, m.director
    FROM Item i left outer join Book b
          on i .ITEM_ID=b.ITEM_ID 
          left outer join
          Album a on i .ITEM_ID=a.ITEM_ID 
          left outer join
          Movie m on i .ITEM ID=m.ITEM ID
- TYPE
  ```java
  예 Item 중에 Book, Movie를 조회하라.
  //JPQL
  select i from Item i
  where type(i) IN (Book, Movie)
  //SQL
  SELECT i FROM Item i 
  WHERE i.DTYPE in ('B', 'M')
  ```
  - 엔티티의 상속 구조에서 조회 대상을 특정 자식 타입으로 한정할 때 주로 사용한다.

- TREAT(JPA 2.1)
  - TREAT는 JPA 2.1 에 추가된 기능인데 자바의 타입 캐스팅과 비슷하다.
  - 상속 구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용한다.
  - JPA 표준은 FROM, WHERE절에서 사용할 수 있지만, 하이버네이트는 SELECT 절에서도 TREAT를 사용할 수 있다.
   ![image.jpg1](./images/10.2_59.PNG)
    -  treat를 사용해서 부모 타입인 Item을 자식 타입인 Book으로 다룬다. 따라서 author 필드에 접근할 수 있다.
    
## 10.2.12 사용자 정의 함수 호출(JPA 2.1)
  ![image.jpg2](./images/10.2_60.PNG) | ![image.jpg1](./images/10.2_61.PNG) 
  |----|----|
   - 하이버네이트 구현체를 사용하면 방언 클래스를 상속해서 구현하고 사용할 데이터베이스 함수를 미리 등록해야한다.
   - 하이버네이트 구현체를 사용하면 select group_concat(i.name) from Item i 이렇게 축약해서 사용할 수 있다.

## 10.2.13 기타 정리
- enum은 = 비교 연산만 지원한다.
- 임베디드 타입은 비교를 지원하지 않는다.
- EMPTY STRING
  - JPA 표준은 "을 길이 0인 Empty String으로 정했지만 데이터베이스에 따라 ‘’를 
    NULL로 사용하는 데이터베이스도 있으므로 확인하고 사용해야 한다.
- NULL 정의
  - 조건을 만족하는 데이터가 하나도 없으면 NULL이다
  - NULL은 알 수 없는 값이다. NULL과의 모든 수학적 계산 결과는 NULL이 된다
  - Null == Null은 알 수 없는 값이다.
  - Null is Null은 참이다.

## 10.2.14 엔티티 직접 사용
- 기본키 값
  - JPQL에서 엔티티 객체를 직접 사용하면 SQL에서는 해당 엔티티의 기본 키 값을 사용한다.
  ![image.jpg1](./images/10.2_62.PNG)
  - count(m)는 엔티티의 별칭을 직접 넘겨줬다.
  - count(m) 처럼 엔티티를 직접 사용하면 JPQL이 SQL로 변환될 때 해당 엔티티의 기본 키를 사용한다.
  -  실제 실행된 SQL은 둘 다 같다
  ![image.jpg1](./images/10.2_63.PNG)
  - JPQL에서 where m = :member로 엔티티를 직접 사용하는 부분이 SQL에서 where m. id=?로 기본 키 값을 사용하도록 변환된 것을 확인할 수 있다
  ![image.jpg1](./images/10.2_64.PNG)
  - 식별자 값을 직접 사용해도 전 결과와 동일하다.
    
- 외래키 값
  ![image.jpg2](./images/10.2_65.PNG) | ![image.jpg1](./images/10.2_66.PNG) 
  |----|----|
  - m.team은 현재 team_id 라는 외래키와 매핑 되어있다
  - 
  ![image.jpg1](./images/10.2_67.PNG)
  - 외래키에 식별자 값을 직접 사용한다.
  - m.team.id를 보면 Member와 Team 간에 묵시적 조인이 일어날 것 같지만 MEMBER 테이블이 team_id 외래 키를 가지고 있으므로 묵시적 조인은 일어나지 않는다
  - 물론 m.team.name을 호출하면 묵시적 조인이 일어난다.
  - m.team을 사용하든 m.team.id를 사용하든 생성되는 SQL은 같다.

## 10.2.15 Named 쿼리 : 정적 쿼리
- JPQL 쿼리는 크게 동적 쿼리와 정적 쿼리로 나눌 수 있다.
  - 동적 쿼리
    - em.createQuery ("select") 처럼 JPQL을 문자로 완성해서 직접넘기는 것을 동적 쿼리라한다.
    - 런타임에 특정 조건에 따라 JPQL을 동적으로 구성할 수 있다
  - 정적 쿼리
    - 미리 정의한 쿼리에 이름을 부여해서 필요할 때 사용할 수 있는데 이것을 Named 쿼리라 한다.
    - 한번 정의하면 변경할 수 없는 정적인 쿼리다.
    - 애플리케이션 로딩 시점에 JPQL 문법을 체크하고 미리 파싱해둔다
    - 오류를 빨리 확인할 수 있고, 사용하는 시점에는 파싱된 결과를 재사용하므로 성능상 이점이 있다.
    - 변하지 않는 정적 SQL이 생성되므로 데이터베이스의 조회 성능 최적화에도 도움이 된다.
    - @NamedQuery 어노테이션을 사용해서 자바 코드에 작성하거나 또는 XML 문서에 작성 할 수 있다.
      
- Named 쿼리를 어노테이션에 정의
  - Named 쿼리는 이름 그대로 쿼리에 이름을 부여해서 사용하는 방법이다.
  
  ![image.jpg1](./images/10.2_68.PNG)
  - @NamedQuery.name에 쿼리 이름을 부여하고 @NamedQuery.query에 사용할 쿼리를 입력한다.
    
  ![image.jpg1](./images/10.2_69.PNG)
  - em.createNamedQuery () 메소드에 Named 쿼리 이름을 입력하면 된다.

  > Named 쿼리 이름을 간단히 findByUsername이라 하지 않고 Member.findByUsername처럼 앞에 엔티티 이름을 준다.
    기능적으로 의미가 있는 건 아니고 영속성 유닛 단위로 관리되므로 충돌을 방지하기 위해 쓴다. 관리하기 쉽다.
  
  ![image.jpg1](./images/10.2_70.PNG)
  - 하나의 엔티티에 2개 이상의 Named 쿼리를 정의하려면 @NamedQueries 어노테이션을 사용한다.

- @NamedQuery 어노테이션
  ![image.jpg1](./images/10.2_71.PNG)
  - lockMode： 쿼리 실행 시 락을 건다.
  - hints：여기서 힌트는 SQL 힌트가 아니라 JPA 구현체에게 제공하는 힌트다. (2차 캐시를 다룰 때 사용한다.)

- Named 쿼리를 XML에 정의
  ![image.jpg1](./images/10.2_72.PNG)
  - JPA에서 어노테이션으로 작성할 수 있는 것은 XML 로도 작성할 수 있다. 물
  -  Named 쿼리를 작성할 때는 어노테이션보다 XML을 사용하는 것이 더 편리하다.
  -  정의한 xml을 인식하도록 META-INF/persistence.xml에 다음 코드를 추가해야 한다.
  > XML에서 &. <,>는 XML 예약문자다. 대신에 &amp;, &lt；, &gt;를 사용해야 한다. <![CDATA[ ]]>를 사용하면 그 사이에 문장을 그대로 출력하므로 예약문자도 사용할 수 있다.

- 환경에 따른 설정
  - 만약 XML과 어노테이션에 같은 설정이 있으면 XML이 우선권을 가진다.
  - 애플리케이션이 운영 환경에 따라 다른 쿼리를 실행해야 한다면 각 환경에 맞춘 XML을 준비해 두고 XML만 변경해서 배포하면 된다.
