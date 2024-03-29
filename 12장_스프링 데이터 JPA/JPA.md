# 12장_스프링 데이터 JPA

<b>✨설명 전 Point 잡고 가기✨</b> 

jpa 편하게 사용하는게 스프링 jpa다. 

```java
 public Optional<Member> findById(Long id) {
   Member member = em.find(Member.class, id);
   return Optional.ofNullable(member);
 }

```
- 반환값이 null일 수 있다. Optional 타입을 한번 감싸서 내보내는거다.
-java 8
- 기본적으로 알쟈
- 반복적인 코드를 인터페이스로 해결
- 스프링 jpa가 알아서 구현체를 만들어서 인젝션해주는거다.
- 로딩시점에 스프링관련된 인터페이스가 있으면 구현체를 만들어버림
- sping data jpa가 JpaRepository를 extends 상속받을 것을 보고 구현체를 만들어서 주입(인젝션)시킨다.
- 인터페이스 레포지토리는 @Repository안해줘도된다.
- 인터페이스만 잡으면 스프링 데이터 jpa가  구현클래스를 만들어서 인젝션한다.

- 중요) 엔티티,엔티티에 매핍된 pk의 타입이다.
- jpa를 편하게 쓸 수 있게 구현코드를 작성할 필요없이 인터페이스만 선언해서 사용하기만 하면된다.
- 구현체 없고 인터페이스만 있다.
- jpa에 특화된 기술이 있는 jpaRepository를 상속 받는다.
- 유사한 인터페이스로 편하게 개발하기 좋다.~
- 구현하지않아도 스프링 데이터 jpa가 쿼리메소드 기능으로 쿼리를 만들지 않아도 구현해준다.

- 공통 메소드가 아니라 내가 따로 구현한 쿼리를 만들고 싶으면 쿼리 메소드 기능을 사용하면 된다. 
- 쿼리 메소드 기능
  - 메소드 이름만 봐도 아는데 우리가 구현해줘야하나..
  - 구현체 안만들고 메소드 이름만 형식에 맞춰서 인터페이스에 구현했더니 실행된다.
  - 스프링 데이터 jpa가 제공하는 아주 강력한 기능인 메소드 이름으로 만들어준다.
  - 이름을 꼭 맞춰줘야한다. 관례에 맞게 구현되야 한다.
  - 맞지 않으면 이름이 맞지 않다는 오류가 난다.
  - 어떤 기능이 되는지는 jpa 메뉴얼을 봐야한다. spring data jpa를 보자!
  - 이름이 너무 길어지면..특히 2개 이상..다른 방법으로 jpql을 직접짜서 한다.
  - 쿼리 메소드 이름을 가지고 원하는 데이터를 뽑아 올 수 있다.
 
 - 엔티티의 필드명을 바꾸면 실행시점에 오류가 나는걸 잡을 수 있다. (애플리케이션 로딩시점에)

 - -----
 JPA NamedQuery는 실무에 쓸 일이 없어서 편하게 보쟈!
 - jpa는 namedQuery를 제공한다.
 - 엔티티에다가 @namedquery를 준다.
 - 재활용이 가능하다. 계속 같은 쿼리를 쓸 수 있다.
 - 구현하는게 귀찮아서.. jpa에서 사용안한다. 그러나 스프링 데이터 jpa에서는 이 구현을 편리하게 하는 방법을 제공한다.
 - @Query어노테이션을 사용하는거다.  @Param도 적어줘야 한다.
 - 명확하게 jpql이 적혀 있을때 @Param이 필요하다.
 - 스프링 데이터 jpa가 @Query 가 없어도 관례로 인해서 실행을 시켜준다. 먼저 namedBy부터 찾고 공통 메소드를 찾는거다. 없으면 메소드 이름으로 찾는다.
 - 이 기능은 실무에서 거의 사용안한다~~ 엔티티에 있는것도 맘에 안들고 따로 빼도 맘에 안들고 왔다갔다가 도싫고 스프링 데이터 jpa가 레포지토리에다가 바로 쿼리를 지정할 수 있는 막강한 기능이 있어서 named는 별루..
 - 바로 jpql을 칠 수 있는데 뭐하로~
 - 레포지토리에 쿼리 정의가 막강하다!
 - 그래도 namedquery의 막강한 장점이 하나가 있다 오타가 있으면 애플리케이션 로딩시점에 파싱을 시켜서 문법 오류를 알려준다. 정적쿼리기때문에 애플리케이션 로딩시점에 파싱을 해볼 수 있다.
 - "" 문자열은 파싱이 안되서 문법이 틀린지 맞는지 잘모르고 사용자가 실행할때 오류가난다...윽
 - ------------------------
 Query,리포지토리 메소드는 정말 막강하다
 - 인터페이스에다가 jpql을 작성할 수 있다.
 - 이 기능이 장점이 많아서 실무에 많이 쓴다.
 - 이름메소드는 메소드 명이 점점 길어져서 2개가 넘어가면 쓰기 힘들어지는데 query 리포지토리에 메소드를 쓰면 간편해쓴다.
 - Query,리포지토리도 애플리케이션 로딩시점에 오류가 발생한다. 그래서 너무 장점이다. @Query는 이름이 없는 쿼리로 애프리케이셔 로딩시점에 파싱을 한다. 그래서 문법 오류가 난다. 좋아하는 기능이고 권장한다.
 - 메소드 이름은 간단할때 쓰고 살짝 복잡해지면 쿼리직접 정의해서 메소드 명을 가져가는게 좋다.
 - 복잡한 정적 쿼리는 쿼리 직접푼다.
 - 동적 쿼리는 querydsl을 써야한다. 실무에서 가장 많이 쓰고 유지보수도 좋다.
    
----------------
Query값 dto 조회하기
- dto를 반환할때는 꼭 new로 생성자 매칭 시켜줘야 한다. 
  @Query("select m.id, m.username, t.name from Member m join m.team t")
  list<memberdto> findMemberDto();
위 처럼 쓰면 안되고
  @Query("select new study.datajpa.dto.MemberDto( m.id, m.username, t.name) from Member m join m.team t")
  list<memberdto> findMemberDto();
  이렇게 써야 한다. (new study.datajpa.dto.MemberDto())
- jpql이 제공하는 문법이고 dto로 반환한다.
- 객체가 생성해주는건 jpql이다.
- querydsl쓰면 이런거 사실또..쉬워진다 쿼리 dsl~~

- ----------파라미터 바인딩
- 위치기반 이름기반
- 위치기반은 안쓴다. 가독성과 유지보수로 이름기반을 써야한다.
- 컬렉션 파라미터 바인딩은 in절로 여러개를 조회하고 싶을때 쓰는 기능인데
- 많이 사용한다.
- @Query("select m from member m where m.username in : names")
- list<member>findByNameds(@Param("names") List<string> names)
- in을 예쁘게 쓸 수 있다. 자동으로 만들어준다.
- 실행할 때 Arrays.asList("aaa","bbb")로 한다.
- 
------------------
컬렉션도 반환되고 optional도 반환되고 여러가지를 반환할 수 있다. 
- 주의해야할 것은 컬렉션을 조회했을때 파라미터를 이상하게 넣어서 데이터값을 못가져왔을 수 있다. empty 컬렉션을 반환한다.
- 빈 컬렉션을 반환한다.
- 스프링 데이터 jpa는 하나를 조회했을 때 없으면 익셉션 터진걸 try catch로 감싸서 null로 반환한다. 자바 8에 옵션널로써~
- -----------------
- totalcount가 필요(page -컨테츠쿼리,totalcount)
- totalcount가 필요 없는 것 더보기 버튼 (slice)
- -------------스프링 데이터 jpa에서 페이징 보쟈!
- 이름자동메소드에 매개변수에 pageable을 넘겨주면된다.
- 스프링 데이터 jpa는 페이지가 1부터 시작하는게 아니라 0부터 시작한다. 주의!
- page로 받으면 totalcount도 같이 날려서 가져온다.
- 개발 생산성 너무 좋다~
- 컨텐트가져오고 싶으면 page.getcontent()해서 가져온다.
- page.gettoatalElemns()가 totalcount다.
- content.size() - 보여줄 데이터수
- page.getTotalElements - 총 데이터 수
- page.getNumber() - page의 번호 (b)
- page.gettotalPages() - 전체 페이지 개수 (2개)
- page.isFirst() : 첫번째 페이지?
- page.hasNext() : 다음 페이지 있니?
- 정리!
- 파라미터 넘길때 pageable 인터페이스 구현애애를 넘긴다. 그러면 페이징 쿼리가된다.
- 반환 타입에 따라 totalcount를 날릴지 말지 결정한다.
- slice는 다음페이지가 있어? 없어? 정도만 가져온다. count쿼리가 없다!!
- 내가 요청하는거보다 +1 개 더가져온다.
- 페이징쿼리가 토탈카운트 쿼리가 데이터 값 전체 가져와야해서 성능이 안나온다. 그래서 토탈카운트 쿼리를 잘 써야할 때가 있따. 그래서 count쿼리를 분리하는 기능을 제공한다.
- 쿼리를 분리해도된다.
- 실무에서 중요하다!!!! 선능!!
- 그래서 count 쿼리를 분리해서
- @query(value = "select m from Member m left join m.team t",
        countQuery = "select count(m) from Member m")
- page<Member> findByAge(int age, pageable pageable);
  이렇게 작성해준다. 
- 주의할점!! 실무 꿀팁!
- page를 api에서 controller에서 바로 반환하면 안된다.
- 엔티티는 절대 외부에 노출시키면 안됨 DTO 에 꼭 넣어줘야한다. API장애난다..
- 페이지를 DTO로 변환해야한다. 간단하게 변경하는 방법은
- page.map(member -> new MemerDto(member.getId(),member.getUsername(),null)
- 이렇게하면 map에서 결과를 얻을 수 있다. dto로 변경됬으면 결과를 api로 반환해도된다. 내부 엔티티가 아니라 dto로 변경됬기때문이다. 이건 유용하다!!
- page를 유지하면서 dto로 반환할 수 있다. 
- ----------------벌크성 수정 쿼리를 보쟈--------------
- @Modifying 어노테이션을 넣어줘야지 없으면 resultList나 singleList를 호출한다. 그러니 어노테이션을 넣어줘야 한다.
- 업데이트 쿼리인데 @Modifying 어노테이션를 안넣어줘? 오류내
- 벌크성 업데이트 주의할점은 벌크연산은 영속성 컨텍스트로 관리가 뭐가 있는지 상관없이 db에 바로 때려버리니까 조심해야 한다.
- 그래서 벌크성 연산 이후에는 영속성 컨텍스트 날려줘야 한다. em.flush()와 em.clear()를 해줘야 한다.
- 근데 벌크연산의 @modifying 어노테이션 옵션에 clear하는 옵션이 있다 celar()해줄 필요없이  @modifying(clearAutomatically = true)해서 영속성 컨텍스를 초기화한다.
- 벌크연산이나 jdbc템플릿이나 마이바티스처럼 쿼리를 날리는건 jpa가 인식을 못한다.그래서 메모리인 영속성 컨텍스트에 있는거랑 안맞는다.이럴땐 clear나 flush를 해줘야 한다.
- -------------------------@EntityGraph--------------------
- fetch join을 사용하려면 jpql을 항상 써야하는거다.
- 엔티티 그래프를 사용하면 jpql을 안짜고 패치조인이된다.
- 오버라이드해서 메소드를 생성하고 @EntityGraph 어노테이션을 사용해서 연관되서 가져올 엔티티도 가져온다.
- @override
- @entitygraph(attributepaths = {"team"})
List<Member> findAll();

이름도 가능하다.
- 오버라이드해서 메소드를 생성하고 @EntityGraph 어노테이션을 사용해서 연관되서 가져올 엔티티도 가져온다.

- @entitygraph(attributepaths = {"team"})
- @query("select * from member m")
List<Member> findAll();
- EntityGraph 을 사용하면 fetch join을 편하게 사용할 수 있다.


- @entitygraph(attributepaths = {"team"})
List<Member> findEntityGraphByUsername();
- EntityGraph 을 사용하면 fetch join을 편하게 사용할 수 있다.

- 간단할 때는 entitygraph를 쓰고 복잡할때는 jpql의 fetch join을 사용한다. 
----------------hint lock 
-------------
화면에 맞춰진 로직(단순)이랑 비즈니스 로직(핵심)을 따로 분리해서 관리하기를 추천한다.  
사용자 정의 리포지토리가 항상 써야하는건 아니다. 
----------------------오디팅~
auditing 은 실무에서 잘 사용한다.
jpa가 @prepersist를 제공한다. @preupdate를 제공한다. -> 하기전에 타는 어노테이션
@mappedsuperclass를 지정해야 실제 받아진다.  
extends로 상속 받아 jpa 이벤트를 가지고 수정 입력에 대해서 입력일시 수정일시 등을 자동으로 값을 넣어줬다 엔티티가 많아져도 상속만 받으면된다. 
근데 스프링 데이터 jpa는 @EnableAuditing을 꼭 넣어줘야 한다. 그래야지 동작한다
@lastmodifieddate,@createDate 어노테이션을 넣어준다. 
@EntityListeners() 이벤트를 받아서 다 처리해준다.

----도메인 클래스 컨버터 잘 안쓴다. 간단한 조회를 할 경우에만 써라!

