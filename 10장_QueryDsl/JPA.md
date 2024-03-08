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
### 10.4.4 결과 조회
### 10.4.5 페이징과 정렬
### 10.4.6 그룹
### 10.4.7 조인
### 10.4.8 서브쿼리
### 10.4.9 프로젝션과 결과 반환
### 10.4.10 수정，삭제 배치 쿼리
### 10.4.11 동적 쿼리 
### 10.4.12 메소드 위임
### 10.4.13 QueryDSL 정리

## 10.5 네이티브 SQL


---------------------------------------
