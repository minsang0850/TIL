# 5.2 검색을 위한 스펙
검색 조건을 다양하게 조합해야 할 때 스펙(Specification)을 사용할 수 있다.
리포지터리나 DAO는 검색 대상을 걸러내는 용도로 스펙을 사용한다.
하지만 실제 스펙은 이런 식으로 구현하지 않는다. <- 모든 애그리거트 객체를 메모리에 보관하기 어렵고, 조회 성능도 안좋을 것이기 때문.

# 5.3 스프링 데이터 JPA를 이용한 스펙 구현
```java
public interface Specification<T> extends Serializable {
    @Nullable
    Predicate toPredicate(Root<T> root,
                          CriteriaQuery<?> query,
                          CriteriaBuilder cb);
}
```
# 5.4 리포지터리/DAO에서 스펙 사용하기
```java
public interface OrderSummaryDao extends Repository<OrderSumamry, String> {
    List<OrderSummary> findAll(Specification<OrderSummary> spec);
}
```

# 5.5 스펙 조합
and, or 을 사용해서 스펙을 조합할 수 있다.
- and: 두 스펙을 모두 충족하는 조건을 표현하는 스펙을 생성한다.
- or: 두 슾게 중 하나 이상 충족하는 조건을 표현하는 스펙을 생성한다.

null 가능성이 있는 스펙 객체와 다른 스펙을 조합해야 할 때가 있다. 이럴 때 where() 메서드를 사용하면 간편하다.
where() 메서드는 스펙 이너테핑스의 정적 메서드로 null을 전달하면 아무 조건도 생성하지 않는 스펙 객체를 리턴하고 null이 아니면 인자로 받은 스펙 객체를 그대로 리턴한다.

# 5.6 정렬 지정하기
- 메서드 이름에 OrderBy를 사용해서 정렬 기준 지정
- Sort를 인자로 전달

# 5.7 페이징 처리하기
스프링 데이터 JPA는 페이징 처리를 위해 Pageable 타입을 이용한다.
Sort 타입과 마찬가지로 find 메서드에 Pageable 타입 파라미터를 사용하면 페이징을 자동으로 처리해 준다.

Pageable 타입은 인터페이스로 실제 Pageable 타입 객체는 PageRequest 클래스를 이용해 생성한다.
Pageable을 사용하는 메서드의 리턴 타입이 Page일 경우 스프링 데이터 JPA는 목록 조회 쿼리와 함께 COUNT 쿼리도 실행해서 조건에 해당하는 데이터 개수를 구한다.
Page는 전체 개수, 페이지 개수 등 페이징 처리에 필요한 데이터도 함께 제공한다.

# 5.8 스펙 조합을 위한 스펙 빌더 클래스
```java
Specification<MemberData> spec = SpecBuilder.builder(MemberData.class)
        .ifTrue(searchRequest.isOnlyNotBlocked(),
                () -> MemberDataSpecs.nonBlocked())
        .ifHasText(...)
        .toSpec();
```

# 5.9 동적 인스턴스 생성
```java
@Query("""
        select new com.myshop.order.query.dto.OrderView(o.number, o.state, m.name, m.id, p.name)
        """)
```
동적 인스턴스의 장점은 JPQL을 그대로 사용하므로 객체 기준으로 쿼리를 작성하면서도 동시에 지연/즉시 로딩과 같은 고민 없이 원하는 모습으로 데이터를 조회할 수 있다는 점이다.

# 5.10 하이버네이트 @Subselect 사용
@Subselect는 쿼리 결과를 @Entity로 매핑할 수 있는 유용한 기능이다.

테이블이 아닌 쿼리 결과를 @Entity로 매핑할 수 있다.
@SubSelect는 조회 쿼리를 값으로 갖는다.
이렇게 조회한 @Entity는 수정할 수 없다.
이슈 방어를 위해 @Immutable을 사용한다.