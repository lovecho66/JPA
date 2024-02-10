# 4장 엔티티 매핑

<b>✨설명 전 Point 잡고 가기✨</b> 

1. JPA를 사용하는데 가장 중요한 일은 엔티티와 테이블을 정확하게 매핑하는 것이다.

2. 매핑 어노테이션을 지원하는데 크게 4가지로 분류해보면 아래와 같다. (대표 어노테이션만 기입)<br>

    1) 객체 - 테이블 매핑 : @Entity ,@Table<br>
    2) 기본키  매핑 : @Id<br>
    3) 필드와 컬럼 매핑 : @Column<br>
    4) 연관관계 매핑 : @ManyToOne, @JoinColum<br>

3. 이번 장에서는 객체와 테이블 매핑, 기본 키 매핑, 필드와 컬럼 매핑을 알아본다.

> 참고 : 매핑 정보는 XML이나 어노테이션 중 선택해서 기술하면 된다.<br>
         각각 장단점이 있지만 어노테이션을 사용하는 쪽이 좀 더 쉽고 직관적이다.
         (책에서는 어노테이션만 기술하고 있다.)
         

## 4.1 @Entity
  1. JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 어노테이션을 필수로 붙여야다.
  2. @Entity가 붙은 클래스는 JPA가 관리하게 된다.
  3. @Entity속성 정리
     |속성|기능|기본값|
     |-----|---------------|-------|
     |name|JPA에서 사용할 엔티티 이름을 지정한다.<br> 만약 다른 패키지에 이름이 같은 엔티티 클래스가 있다면 name 속성을 가지고 이름을 지정해서 충돌하지 않도록 해야한다.|클래스 이름을 그대로 사용한다.|
  4. @Entity 주의사항
     1) JPA가 엔티티를 생성할 때 기본생성자를 사용하므로 __기본 생성자는 필수__ 로 있어야 한다.
        ```java 
        // 1. 직접 만든 기본 생성자
        public Member(){}
      
        // 2. 임의의 생성자
        public Member(String name){
          this.name = name;
        }  
        ```
        - 자바는 생성자가 하나도 없으면 기본 생성자를 자동으로 만들어준다.
        - 근데 기본 생성자를 사용하지 않고 따로 생성자를 만들게 되면 자바가 기본생성자를 만들어주지 않는다.
        - 그래서 JPA가 기본생성자를 사용할 수 있게 코드1번과 같이 기본생성자를 추가로 만들어줘야 한다. 
        
     2) __final 클래스,enum,interface,inner 클래스에서는 사용할 수 없다.__ <br>
        ![개념및질문](./QUESTION.md) - 개념 1번
     3) __저장할 필드에 final을 사용하면 안된다.__
        ![개념및질문](./QUESTION.md) - 질문 1번
        
## 4.2 @Table
1. @Table 어노테이션은 엔티티와 매핑할 테이블을 지정해준다.
2. 생략하면 매핑한 엔티티 이름을 테이블 이름으로 사용한다.
3. @Table 속성 정리
    |속성|기능|기본값|
    |-----|---------------|-------|
    |name |매핑할 테이블 이름을 지정한다. |엔티티 이름을 사용한다. |
    |catalog|catalog 기능이 있는 데이터베이스에서 catalog를 매핑한다.| |
    |스키마|schema 기능이 있는 데이터베이스에 schema를 매핑한다. | |
    |uniqueConstraints(DDL)|DDL 생성 시에 유니크 제약조건을 만든다.<br>2개 이상의 복합 유니크 제약조건도 만들 수 있다. <br> 이 기능은 스키마 자동 생성 기능을 사용해서 DDL을 만들 때만 사용된다. | |
   
## 4.3 다양한 매핑 사용
```java 
package jpabook.start;
import javax.persistence.*
import java.util.Date;

@Entity
@Table(name = "MEMBER")
public class Member{

    @Id
    @Column(name = "NAME")
    private String id;

    @Column(name ="NAME")
    private String userName;

    private Integer age;

    @Enumerated(EnumType.String)
    private RoleType roleType;       //①

    @Temporal(TemporalType.TIMESTAMP)
    private Date createDate;         //②

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    @Lob
    private String description       //③

    //Getter,Setter
    ...

}

public enum RoleType(){
    ADMIN, USER
}
```
소스코드 보면<br>
① roleType : 자바의 enum을 사용해서 회원 타입을 구분했다. enum을 사용하려면 @Enumerated 어노테이션을 매핑해야한다.<br>
 ![개념및질문](./QUESTION.md) - 개념 2번<br>
② createdData, lastModifiedDate : 자바의 날짜 타입은 @Temporal을 사용해서 매핑한다. <br>
③ description : 설명하는 필드는 길이 제한이 없다. 데이터베이스의 VARCHAR 타입대신 CLOB으로 저장해야 한다. @Lob을 사용하면 CLOB,BLOB 타입을 매핑할 수 있다.

## 4.4 데이터베이스 스키마 자동 생성
1. JPA는 데이터베이스 스키마를 자동으로 생성하는 기능을 지원한다.
2. JPA는 매핑정보와 데이터베이스 방언을 사용해서 데이터베이스 스키마를 생성한다.
   (스키마를 생성한다? : 직접 생성은 아니고 엔티티 클래스 기반으로 데이터베이스 테이블을 생성하고 관리하며 뷰를 매핑하고 관리하는 것이 가능해서 저렇게 표현한듯)
3. 스키마 자동 생성 기능을 사용하려면 persistence.xml에 아래 속성을 추가해야한다.
```xml
<property name = "hibernate.hbm2ddl.auto" value = "create"/>
```
 - 위 속성을 추가하면 애플리케이션 실행 시점에 데이터베이스 테이블을 자동으로 생성한다.
4. hibernate.show_sql 속성을 true로 설정하고 콘솔에 실행되는 테이블의 생성 DDL을 출력해보면 아래와 같다.
```txt
Hibernate:
drop table MEMBER if exists 
Hibernate:
create table MEMBER (
    ID varchar(255) not null, 
    NAME varchar(255), 
    age integer, 
    roleType varchar(255), 
    createdDate timestamp, 
    lastModifiedDate timestamp, 
    description clob, 
    primary key (ID)
)
```
- 출력된 DDL을 확인해보면 기존 테이블을 삭제하고 다시 생성한 것을 볼 수 있다.
- 위 예시는 MYSQL의 DDL이고 이는 데이터베이스 방언에 따라서 자동 생성되는 DDL은 달라진다.
5. 스키마 자동 생성 기능이 만든 DDL은 운영환경에서 사용할 만큼 완벽하지 않다. 그래서 개발 환경이나 매핑작업을 할 때 참고하는 정도로만 사용하자.
6. hibernate.hbm2ddl.auto 속성 정리
    |옵션|설명|
    |------|---------------|
    |create|기존 테이블을 삭제하고 새로 생성한다.(DROP + CREATE)|
    |create-drop|create 속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거한다.(DROP + CREATE + DROP)|
    |update|데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경사항만 수정한다.|
    |validate|데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다.<br>이 설정은 DDL을 수정하지 않는다.|
    |none|유효하지 않는 옵션 값으로 자동 생성 기능을 사용하지 않는다.<br>none으로 옵션을 주거나 아니면 hibernate.hbm2ddl.auto 속성 자체를 삭제해도 좋다.|
7. HBM2DDL 사용시 주의사항
  - 운영 서버에서 create,create-drop,update처럼 ddl을 수정하는 옵션은 절대 사용하면 안된다. 운영중인 데이터베이스의 테이블이나 컬럼이 삭제될 수 있다.
  - 꼭 개발 서버나 개발 단계에서만 사용해야한다.
  - 추천 전략
    - 개발 초기 단계에는 create 또는 update 설정한다.
    - 초기화 상태로 자동화된 테스트를 진행하는 개발 환경과 CI서버에는 create 또는 create-drop을 설정한다.
    - 테스트 서버는 update 또는 validate를 설정한다.
    - 스테이징과 운영서버는 validate 또는 none을 설정한다.
8. JPA는 2.1부터 스키마 자동생성 기능을 표준으로 지원한다.
    - none,create,create-drop,drop 옵션 지원
    - 하이버네이트의 hibernate.hbm2ddl.auto 속성이 지원하는 update,validate 옵션은 지원하지 않는다.
    - 
## 4.5 DDL 생성 기능
## 4.6 기본 키 매핑
## 4.7 필드와 컬럼 매핑 : 레퍼런스
## 4.8 정리
## 실전 예제 | 1. 요구사항 분석과 기본 매핑
