# 6장_다양한 연관관계 매핑

<b>✨설명 전 Point 잡고 가기✨</b> 
 
 - 다중성과 단방향,양방향을 고려한 모든 연관관계를 본다. 

## 6.1 다대일
- 객체 양방향 관계에서 연관관계의 주인은 항상 다쪽이다.

## 6.2.1 다대일 단방향 [N:1]

![image.jpg1](./images/6.1_1.PNG)

- Team 하나에 Member 여러명이 포함되는 관계다.

![image.jpg1](./images/6.1_2.PNG) |![image.jpg2](./images/6.1_3.PNG)
--- | --- | 
- Member는 Team을 참조할 수 있지만 Team에서는 Member를 참조하는 필드가 없다.
- 따라서 Member와 Team은 다대일 단방향 연관관계다.

```java
@ManyToOne
@JoinColumn(name ="TEAM_ID")
private Team team;
```
- @JoinColumn(name ="TEAM_ID")로 team필드와 MEMBER 테이블의 TEAM_ID 외래키와 매핑했다.

## 6.2.1 다대일 양방향 [N:1,1:N]

![image.jpg1](./images/6.1_4.PNG)
- 실선이 연관관계의 주인이고 점선은 연관관계의 주인이 아니다.

![image.jpg1](./images/6.1_5.PNG)|![image.jpg2](./images/6.1_6.PNG)
--- | --- | 

- Team에서 Member를 참조할 수 있는 members 필드가 있다.
- Member에서 Team을 참조하고 Team에서 Member도 참조할 수 있다.
- 서로가 서로를 참조할 수 있으므로 이 관계는 양방향 연관관계다.
- 양방향 연관관계 특징
  - 양방향은 외래키가 있는 쪽이 연관관계의 주인이다.
    - 일대다와 다대일 연관관계는 항상 다(N)에 외래 키가 있다.
    - JPA는 외래 키를 관리할 때 연관관계의 주인만 사용한다. (주인아닌것은 JPA에게 뭘해도 무시되자녀)
    - (주인이 아닌것은 그럼 왜 존재하냐?) 주인이 아닌 Team.members는 조회를 위한 JPQL이나 객체 그래프 탐색을 사용한다.
  - 양방향 연관관계는 항상 서로를 참조해야한다.
    ```java
    public void setTeam(Team team){
      this.team = team;
      //무한루프에 빠지지 않도록 체크
      if(!team.getMembers().contains(this)){
        team.getMember().add(this);
      }
    }
    ```
    - 항상 서로 참조하게 하려면 연관관계 편의 메소드를 작성하는 것이 좋다.
    - 연관관계 편의 메소드는 한곳에만 작성하거나 양쪽 다 작성할 수 있는데, 양쪽 모두 작성하면 무한루프에 빠지므로 주의해야한다.
## 6.2 일대다
- 일대다 관계는 엔티티를 하나 이상 참조할 수 있으므로 자바 컬렉션인 Collection,List,Set,Map 중 하나를 사용한다.
### 6.2.1 일대다 단방향
![image.jpg1](./images/6.2_1.PNG)
- 일대다 단방향 관계는 특이점이 있다.
- 보통 자신이 매핑한 테이블의 외래키를 관리하는데 이 매핑은 반대쪽 테이블에 있는 외래키를 관리한다.
- Member 테이블에 외래키가 있어서 일반적으로 Member 엔티티에 외래키를 관리하게끔 하는데 외래키를 매핑할 수 있는 참조 필드가 없다.
- 반대쪽 Team 엔티티에서만 Member를 참조할 수 있는 필드가 존재한다.
  
![image.jpg1](./images/6.2_2.PNG)|![image.jpg2](./images/6.2_3.PNG)
--- | --- | 
- 일대다 단방향 관계를 매핑할 때는 @JoinColumn을 명시해야한다.
- @JoinColumn을 명시하지 않는다면 JPA는 연결 테이블을 중간에 두고 연관관계를 관리하는 조인 테이블 전략을 기본으로 사용해서 매핑한다. (7.4절)
![image.jpg1](./images/6.2_4.PNG)|![image.jpg2](./images/6.2_5.PNG)
--- | --- | 
- 일대다 단방향 매핑의 단점은 매핑한 객체가 관리하는 외래키가 다른 테이블에 있다는 점이다.
- 본인 테이블에 외래키가 있으면 엔티티의 저장과 연관관계 처리를 INSERT SQL 한번으로 끝낼 수 있지만
  다른 테이블에 외래키가 있으면 연관관계를 처리하기 위한 UPDATE SQL을 추가로 실행해야한다.
-
## 6.1 일대일
## 6.1 다대다





