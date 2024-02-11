# 5장 연관관계 매핑 기초

<b>✨설명 전 Point 잡고 가기✨</b> 

1. 객체의 참조와 테이블의 외래키를 매핑하는 것이 5장의 목표다.
2. 연관관계 매핑을 이해하기 위한 핵심 키워드를 보면 방향,다중성,연관관계의 주인이 있다.
  1) 방향은 단방향과 양방향 두개가 있는데 둘 중 한쪽만 참조하는 것을 단방향 관계라고 하고 양쪽 모두 서로 참조하는 것을 양방향 관계라고 한다.
     (방향은  객체관계에서만 존재하고 테이블은 항상 양방향이다.)
  2) 다중성은 다대일,일대다,일대일,다대다 로 표현할 수 있다.
  3) 연관관계의주인은 객체를 양방향 연관관계로 만들면 연관관계의 주인을 정해야한다.

## 5.1 단방향 연관관계
### 5.1.1 순수한 객체 연관관계
```java
public class Member {
  private String id; 
  private String username;
  private Team team; //팀의 참조 보관

  public void setTeam(Team team) { 
    this,team = team;
  }
//Getter, Setter ...
```
1. Member 엔티티가 Team 엔티티를 참조할 수 있게 설정했다.
![ConnectionMaker](./images/5.3.PNG)   
2.객체는 참조를 사용해서 연관관계를 탐색할 수 있다.(객체 그래프 탐색)
### 5.1.2 테이블 연관관계
```sql
CREATE TABLE MEMBER (
  MEMBER_ID VARCHAR(255) NOT NULL, 
  TEAM_ID VARCHAR(255),
  USERNAME VARCHAR(255),
  PRIMARY KEY (MEMBER_ID)
)
CREATE TABLE TEAM (
  TEAM_ID VARCHAR(255) NOT NULL, 
  NAME VARCHAR(255),
  PRIMARY KEY (TEAM_ID)
)
ALTER TABLE MEMBER ADD CONSTRAINT FK_MEMBER_TEAM 
  FOREIGN KEY (TEAM_ID)
  REFERENCES TEAM
```
1. 테이블 생성시 외래키도 설정해준다.
```sql
INSERT INTO TEAM(TEAM_ID, NAME) VALUES('teaml','팀') ;
INSERT INTO MEMBER(MEMBER_ID, TEAM_ID, USERNAME)
VALUES ('member1','team1','회원1')
INSERT INTO MEMBER(MEMBER_ID, TEAM_ID, USERNAME)
VALUES ('member2','team1','회원2')
```
2. 외래키 제약조건에 맞게 데이터를 생성한다.
```sql
SELECT T,*
FROM MEMBER M
JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID 
WHERE M.MEMBER_ID =  'member1'
```
3. 데이터베이스는 외래키를 사용해서 연관관계를 탐색할 수 있다. 이를 join이라고 한다.
### 5.1.3 객체 관계 매핑
![ConnectionMaker](./images/5.4.PNG)   
```java
@Entity
public class Member {
  @Id
  @Column(name = "MEMBER_ID"〉 
  private String id;
  private String username;

  //연관관계매핑
  @ManyToOne
  @ JoinColumn (name="TEAM_ID"}
  private Team team;

  //연관관계설정
  public void setTeam(Team team) { 
  this.team = team;
  }
  //Getter, Setter ...
}
```
```java
@Entity
public class Team {
  @Id
  @Column(name = "TEAM ID") 
  private String id;
  private String name;
  //Getter, Setter ...
}
```
1. 객체 연관관계 설정시 Member객체에 team 필드를 사용했고 테이블 연관관계 설정시 회원 테이블의 TEAM_ID 외래키 컬럼을 사용했다.
2. Member.team과 MEMBER.TEAM_ID를 매핑하는 것이 연관관계 매핑이다.
3. 객체와 테이블의 연관관계를 매핑하기 위해 @ManyToOne 과 JoinColumn(name="TEAM_ID")를 사용한다.
### 5.1.4 JoinColumn
1. 외래키를 매핑할 때 사용한다.
2. @JoinColumn 주요 속성
    |속성|기능|기본값|
    |-----|---------------|-------|
    |name |매핑할 외래캐 이름을 지정한다. |필드명_참조하는 테이블의 기본키 컬럼명|
    |referencedColumnName |외래 키가 참조하는 대상 테이블의 컬럼명|참조하는 테이블의 기본 키 컬럼명|
    |foreignKey(DDL) |외래 키 제약조건을 직접 지정할 수 있다. 이 속성은 테이블을 생성할 때만 사용한다.| |
    |unique<br>nullable<br>insertable<br>updatable<br>columnDefinition<br>table|@Column의 속성과 동일하다.| |
3. @JoinColumn을 생략하면 [필드명_참조하는 테이블의 기본키 컬럼명]기본값으로 team_TEAM_ID 외래키를 사용한다.
### 5.1.5 @ManyToOne
1. 연관관계를 매핑할 때 이렇게 다중성을 나타내는 어노테이션은 필수인데 그중 @ManyToOne은 다대일 관계를 표현한다.
2. @ManyToOne 속성
    |속성|기능|기본값|
    |-----|---------------|-------|
    |optional |false로 설정하면 연관된 엔티티가 항상 있어야 한다.|true|
    |fetch |글로벌 페치 전략을 설정한다.| @ManyToOne=FetchType.EAGER(즉시로딩)<br>@OneToMany=FetchType.LAZY(지연로딩)|
    |cascade |영속성 전이 기능을 사용한다.| |
    |targetEntity |연관된 엔티티의 타입 정보를 설정한다. 컬렉션을 사용해도 제네릭으로 타입 정보를 알 수 있어서 잘 사용하지 않는다.| |

```java
@OneToMany
private List<Member> members;  // 제네릭으로 타입 정보를 알 수 있다.
@OneToMany(targetEntity=Member.class)
private List members; //제네릭이 없으면 타입 정보를 알 수 없다.
```
4. 제너릭으로 타입정보를 지정하면 @targetEntity로 따로 반환타입을 설정하지 않아도 알 수 있다.
   
## 5.2 연관관계 사용
### 5.2.1 저장
```java
public void testSave() {
  //팀1 저장
  Team teaml = new Team("teaml", "팀1"〉; 
  em.persist(teaml);

  / / 회원 1 저장
  Member member1 = new Member("member1","회원1");
  memberl.setTeam(teaml) ; //연관관계 설정 member1 -> teaml 
  em.persist(member1);
  //회원2 저장
  Member member2 = new Member ("member2","회원2") ; 
  member2.setTeam(teaml); / / 연관관계 설정 member2 -> teaml 
  em.persist(member2);
}
```
1. 연관관계 사용시 저장은 특별한거 없이 Member 객체와 관련된 Team객체를 setTeam으로 member와 team의 연관관계를 설정해주면된다.
```sql
INSERT INTO TEAM (TEAM_ID, NAME) VALUES ('teaml','팀1')
INSERT INTO MEMBER (MEMBER_ID, NAME, TEAM_ID) VALUES ('member1','회원1', 'teaml')
INSERT INTO MEMBER (MEMBER_ID, NAME, TEAM_ID) VALUES ('member2','회원2', 'teaml')
```
2. 등록 쿼리를 생성하게되면 위와 같은데 회원 테이블의 외래키 값으로 참조한 팀의 식별자 값인 team1이 입력된다.
### 5.2.2 조회
1. 연관관계가 있는 엔티티를 조회하는 방법은 크게 객체 그래프 탐색(객체 연관관계를 사용한 조회)와 객체지향 쿼리 사용(JPQL) 두가지가 있다.
```java
Member member = em.find (Member.class,"memberl") ;
Team team = member. getTeam(); / / 객체 그래프 탐색 
System.out.println ("팀 이름 = " + team. getName()) ; //팀1
```
2. 저장시 연관관계 설정을 해주면 meber.getTeam()을 사용해서 member와 연관된 team 엔티티를 조회할 수 있다.
```java
private static void queryLogicJoin(EntityManager em) {

String jpql = "select m from Member m join m.team t where" +t.name=: teamName;

List<Member> resultList = em.createQuery(jpql, Member.class) .setParameter("teamName", "팀1").getResultList() ;

  for (Member member : resultList) {
  System.out.println ("[query] member.username="+ member.getUsername());
  }
}
//결 과 : [query] member.username:회원 1 
//결 과 : [query] member.username=회원2
```
3. JPQL에서 회원이 팀과 관계를 가지고 있는 필드(m.team)를 통해서 Member와 Team조인을 했다.
### 5.2.3 수정
```java
private static void updateRelation(EntityManager em) {
  //새로운 팀2
  Team team2 = new Team("team2","팀2") ; 
  em.persist(team2);

  // 회원 1 에 새로운 팀2 설정
  Member member = em.find (Member.class"memberl") ; 
  member.setTeam(team2);
}
```
1. 연관관계 사용 수정도 불러온 엔티티의 값만 변경해두면 트랜잭션 커밋할때 플러시가 일어나면서 변경 감지 기능이 작동한다. 그래서 변경사항을 데이터베이스에 자동으로 반영한다. 이렇게 참조대상(team1->team2)만 변경해주면 JPA가 자동으로 처리한다.
### 5.2.4 연관관계 제거
```java
private static void deleteRelation(EntityManager em) {
  Member member1 = em.find(Member.class,"memberl") ; 
  member1.setTeam(null) ; //연관관계 제거
}
```
```sql
UPDATE MEMBER 
SET TEAM_ID=null,
WHERE ID='member1'
```
1. 연관관계 제거하는 것도 특별한거 없이 참조하는 대상을 null로 설정해 연관관계도 null로 만드는거다.
### 5.2.5 연관관계 삭제
```java
memberl.setTeam(null) ; //회원1 연관관계 제거 
member2.setTeam(null) ; //회원2 연관관계 제거 
em.remove (team) ; //팀 삭제
```
1. 연관된 엔티티를 삭제하려면 기존에 있던 연관관계를 먼저 제거하고 삭제해야 한다. 그렇지 않으면 외래키 제약조건으로 인해 오류가 발생한다.
2. 연관관계 제거후 삭제한다.
## 5.3 양방향 연관관계
## 5.4 연관관계의 주인
## 5.5 양방향 연관관계 저장
## 5.6 양방향 연관관계 주의점
