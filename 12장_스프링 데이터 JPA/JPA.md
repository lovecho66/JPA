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
