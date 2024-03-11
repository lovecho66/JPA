# 10장_QueryDSL

<b>✨설명 전 Point 잡고 가기✨</b> 

1. QueryDSL은 쿼리를 문자가 아닌 코드로 작성해도, 쉽고 간결하며 그 모양도 쿼리와 비슷하게 개발할 수 있게 해준다. 
2. QueryDSL도 JPQL 빌더 역할을 하기때문에 JPA Criteria를 대체할 수 있다
3. QueryDSL은 오픈소스 프로젝트다.
4.  처음에는 HQL(하이버네이트 쿼리언어)을 코드로 작성할 수 있도록 해주는 프로젝트로 시작해서 지금은 JPA, JDO, JDBC, 
Lucene, Hibernate Search, 몽고DB, 자바 컬렉션 등을 다양하게 지원한다.

## 10.4.1 QueryDSL 설정

(pom.xml)

![ConnectionMaker](./images/10.4_1.PNG)   
- querydsl-jpa : QueryDSL JPA 라이브러리
- querydsl-apt : 쿼리타입(Q)을 생성할 때 필요한 라이브러리

### 환경설정
- QueryDSL는 엔티티를 기반으로 쿼리 타입이라는 쿼리용 클래스를 생성해야한다.

![ConnectionMaker](./images/10.4_2.PNG)   

- 쿼리 타입 생성용 플러그인을 pom.xml에 위에 처럼 추가한다.
- 콘솔에서 mvn compile을 입력하면 outputDirectory에 지정한 target/generated-sources 위치에 QMember. java처럼 Q로 시작하는 쿼리 타입들이 생성 된다.
- target/generated-sources를 소스 경로에 추가하면 된다.

## 10.2 시작

![ConnectionMaker](./images/10.4_3.PNG)   
- QueryDSL을 사용하려면 우선 com.mysema.query.jpa.impl.JPAQuery 객체를 생성해야 한다.
- JPAQuery 객체를 생성할 때 엔티티 매니저를 생성자에 넘겨준다.
- 사용할 쿼리 타입(Q)을 생성하는데 별칭을 만들어서 생성자에 넘겨준다. 
- 이 별칭은 JPQL에서 별칭으로 사용된다.

### 기본 Q 생성

![image.jpg1](./images/10.4_4.PNG) |![image.jpg2](./images/10.4_5.PNG)
|----|----|
- 쿼리 타입(Q)을 사용하기 편리하도록 기본 인스턴스를 보관하고 있다.
- 단, 같은 엔티티를 조회하거나 조인하거나 같은 엔티티를 서브쿼리에서 사용하면 같은 별칭이 사용되므로 이때는 별칭을 직접 지정해줘야한다.
- 쿼리 타입의 기본 인스턴스를 사용하면 import static을 활용해서 코드를 더 간결하게 작성할 수 있다. 

![ConnectionMaker](./images/10.4_5.PNG)    

- 쿼리 타입을 기본 인스턴스로 보관해서 사용하면 QMember.member가 아닌 member로 사용할 수 있게 된다. 

### 10.4.3 검색 조건 쿼리

![image.jpg1](./images/10.4_6.PNG) |![image.jpg2](./images/10.4_7.PNG)
|----|----|

- QueryDSL의 where 절에는 and 나 or을 사용할 수 있다. 또한 다음처럼 여러 검색 조건을 사용해도 된다. 이 때는 and 연산이 된다.
- .where(item.name.eq("좋은상품"),item.price.gt(20000)) 로 and를 쓰지 않고 ,로도 표현해서 사용할 수 있다.

![ConnectionMaker](./images/10.4_8.PNG)   

- where()에서 사용되는 여러 메소드 들이 있다.
- IDE가 제공하는 코드 자동 완성 기능의 도움을 받으면 필요한 메소드를 손쉽게 찾을 수 있다.

### 10.4.4 결과 조회

- 쿼리 작성이 끝나고 결과 조회 메소드를 호출하면 실제 데이터베이스를 조회한다.
- uniqueResult()나 list()를 사용하고 파라미터로 프로젝션 대상을 넘겨준다.
- 결과 조회 API는 com.myserna.query.Projectable에 정의되어 있다.
- 대표 결과 조회 메소드가 아래와 같다.
  - uniqueResult() : 조회 결과가 한 건일 때 사용한다. 조회 결과가 없으면 null을 반환하고 결과가 하나 이상이면 com.mysema.query . NonUniqueResultException 예외가 발생한다.
  -  singleResult() : uniqueResult()와 같지만 결과가 하나 이상이면 처음 데이터를 반환한다.
  -  list() : 결과가 하나 이상일 때 사용한다. 결과가 없으면 빈 컬렉션을 반환한다.
    
### 10.4.5 페이징과 정렬

![ConnectionMaker](./images/10.4_9.PNG)   

- 정렬은 orderBy를 사용하는데 쿼리 타입(Q)이 제공하는 asc(),desc ()를 사용한다.
- 페이징은 offset과 limit을 적절히 사용해서 사용하면 된다.

![ConnectionMaker](./images/10.4_10.PNG)   
- restrict () 메소드에 com.mysema.query.QueryModifiers를 파라미터로 사용해도 된다.

- 실제 페이징 처리를 하려면 검색된 전체 데이터 수를 알아야 한다.

![ConnectionMaker](./images/10.4_11.PNG)   
- list() 대신에 listResults()를 사용한다.
- listResults()를 사용하면 전체 데이터를 조회를 위한 count 쿼리를 한번 더 실행한다.
- SearchResults()를 반환하는데 이 객체에서 전체 데이터 수를 조회할 수 있다.
  
### 10.4.6 그룹

![ConnectionMaker](./images/10.4_12.PNG)   

- groupBy를 사용하고 그룹화된 결과를 제한하려면 having을 사용하면 된다.

### 10.4.7 조인
![ConnectionMaker](./images/10.4_13.PNG)   
- 조인(join)은 innerJoin (join) leftJoin , rightJoin, fullJoin 을 사용할 수 있고 추가로 JPQL의 on과 성능 최적화를 위한 fetch 조인도 사용할 수 있다.
- 조인을 사용할 때 첫 번째 파라미터에 조인 대상을 지정하고, 두 번째 파라미터에 별칭alias으로 사용할 쿼리 타입을 지정한다.

### 조인 on
![ConnectionMaker](./images/10.4_14.PNG)   

- on 조건을 걸어 join할 대상의 조건을 걸어준다. 

![ConnectionMaker](./images/10.4_15.PNG)   

- join에 페치 조인을 사용할 수 있다. 


![ConnectionMaker](./images/10.4_16.PNG)   

- from 절에 여러 조인을 사용하는 세타 조인 방법이다.

<details>
<summary>세타 조인</summary></summary>

<!-- summary 아래 한칸 공백 두어야함 -->
세타 조인(Theta Join)은 일반적인 조인 유형 중 하나로, 조인 조건에 특정 연산자(보통 등호(=) 외의 연산자)를 사용하여 조인하는 것을 말합니다

</details>

### 10.4.8 서브쿼리

![ConnectionMaker](./images/10.4_17.PNG)|![ConnectionMaker](./images/10.4_18.PNG)  
|------|------|

- com.mysema.query.jpa.JPASubQuery를 생성해서 사용한다.
- 서브쿼리 결과가 하나면 unique(), 여러 건이면 list()를 사용할 수 있다. 

### 10.4.9 프로젝션과 결과 반환

- select 절에 조회 대상을 지정하는 것을 프로젝션이라 한다.

#### 프로젝션 대상이 하나
![ConnectionMaker](./images/10.4_19.PNG)  
- 프로젝션이 하나면 해당 타입으로 반환한다.

#### 프로젝션 대상이 여러개
![ConnectionMaker](./images/10.4_20.PNG)  

- 프로젝션 대상으로 여러 필드를 선택하면 QueryDSL은 기본으로 com.mysemaquery.Tuple이라는 Map과 비슷한 내부 타입을 사용한다.
- 조회 결과는 tuple.get() 메소드에 조회한 쿼리타입을 지정하면 된다.

- 쿼리결과를 엔티티가 아닌 특정 객체로 받고싶으면 빈 생성 기능을 사용한다.
- QueryDSL은 객체를 생성하는 다양한 방법을 제공한다.
  - 프로퍼티 접근
  - 필드 직접 접근
  - 생성자 사용
- 원하는 방법을 지정하기 위해 com.mysem.query, types.Projections를 사용하면 된다.

#### 프로퍼티 접근
![ConnectionMaker](./images/10.4_21.PNG)  
- Projections.bean () 메소드는 수정자(Setter)를 사용해서 값을 채운다.
-  쿼리 결과는 name인데 ItemDTO는 username 프로퍼티를 가지고 있다. 이렇게 쿼리 결과와 매핑 프로퍼티 이름이 다르면 as 별칭을 주면된다.

![ConnectionMaker](./images/10.4_22.PNG)  
- Projections.fields() 메소드를 사용하면 필드에 직접 접근해서 값을 채워준다.
- 필드를 private로 설정해도 동작한다.


![ConnectionMaker](./images/10.4_23.PNG)|![ConnectionMaker](./images/10.4_24.PNG)  
|------|------|

-  Projections.constructor() 메소드는 생성자를 사용한다.
-  프로젝션과 파라미터 순서가 같은 생성자가 필요하다.

#### Distinct

```java
query.distinct().from(item)...
```
### 10.4.10 수정，삭제 배치 쿼리
- QueryDSL도 수정，삭제 같은 배치 쿼리를 지원한다.
- JPQL 배치 쿼리와 같이 영속성 컨텍스트를 무시하고 데이터베이스를 직접 쿼리를 실행한다.
<details>
<summary>배치 쿼리 </summary>

<!-- summary 아래 한칸 공백 두어야함 -->

JPQL 배치 쿼리는 JPA(Java Persistence API)에서 제공하는 기능 중 하나입니다. 이는 대량의 데이터를 처리할 때 성능을 향상시키는 방법 중 하나다.

JPQL 배치 쿼리를 사용하면 일괄 업데이트 또는 삭제와 같은 대량 작업을 처리할 때 단일 쿼리를 실행하여 모든 엔티티를 처리할 수 있습니다. 이를 통해 데이터베이스와의 통신 횟수를 줄이고 처리 시간을 단축할 수 있습니다.

</details>
  
### 10.4.11 동적 쿼리 
### 10.4.12 메소드 위임
### 10.4.13 QueryDSL 정리

## 10.5 네이티브 SQL

## 10.6 객체지향 쿼리 심화


---------------------------------------
