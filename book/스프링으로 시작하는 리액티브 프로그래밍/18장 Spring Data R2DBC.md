## 18.1 R2DBC란?
F2DBC는 관계형 데이터베이스에 리액티브 프로그래밍 API를 제공하기 위한 개방형 사양이면서, 드라이버 벤더가 구현하고 클라이언트가 사용하기 위한 SPI 입니다.

## 18.2 Spring Data R2DBC란?
R2DBC 기반 Repository를 좀 더 쉽게 구현하게 해 주는 Spring Data Family 프로젝트의 일부입니다.
Spring Data R2DBC는 SPring이 추구하는 추상화 기법이 적용되었습니다.

Spring Data R2DBC는 JPA 같은 ORM 프레임워크에서 제공하는 캐싱, 지연 로딩, 기타 ORM 프레임워크에서 가지고 있는 특징들이 제거되어 단순하고 심플한 방법으로 사용할 수 있습니다.

## 18.3 Spring Data R2DBC 설정
### build.gradle
```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-r2dbc'
}
```

### 테이블 스키마 정의
Spring Data R2DBC는 Spring Data JPA 처럼 엔티티에 정의된 매핑 정보로 테이블을 자동 생성해 주는 기능이 없기 때문에 테이블 스크립트를 직접 작성해서 테이블을 생성해야 합니다.

### application.yml 파일 설정
```yaml
spring:
  sql:
    init:
      schema-locations: classpath*:db/h2/schema.sql
      data-locations: classpath*:db/h2/data.sql
logging:
  level:
    org:
      springframework:
        r2dbc: DEBUG
```

### R2DBC Repository와 Auditing 기능 활성화
```java
@EnableR2dbcRepositories
@EnableR2dbcAuditing
@SpringBootApplication
public class Chapter18Application {
    public static void main(String[] args) {
        SpringApplication.run(Chapter18Application.class, args);
    }
}
```

### 18.4 Spring Data R2DBC에서의 도메인 엔티티 클래스 매핑
@Id, @CreadtedDate 등의 필드 사용

## 18.5 R2DBC Repositories를 이용한 데이터 액세스
```java
@Repository("bookRepositoryV5")
public interface BookRepository extends ReactiveCrudRepository<Book, Long> {
    Mono<Book> findByIsbn(String isbn);
}
```
ReactiveCrudRepository 상속, 쿼리 메서드의 리턴 타입이 Mono or Flux

## 18.6 R2dbcEntityTemplate을 이용한 데이터 액세스
가독성 좋은 SQL 쿼리문을 작성하는 것과 같은 자연스러운 방식으로 메서드를 조합하여 데이터베이스와 인터랙션할 수 있는 R2dbcEntityTemplate

Terminating method, Criteria method 사용 가능

## 18.7 Spring Data R2DBC에서의 페이지네이션(Pagination) 처리
### Repository에서의 페이지네이션
Spring Data 패밀리 프로젝트에서 사용하는 Pageable을 이용해서 페이지네이션을 적용하는 방법은 동일하지만, 리턴 타입이 List<Book>이 아니라 Flux<Book>이라는 차이점이 있습니다.

### R2dbcEntityTemplate에서의 페이지네이션 처리
limit(), offset(), sort() 등의 쿼리 빌드 메서드를 조합해서 페이지네이션을 처리합니다.


Reactor의 skip(), take()를 사용해서도 페이징 처리 가능