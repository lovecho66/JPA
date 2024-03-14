# 8장_프록시와 연관관계 관리

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