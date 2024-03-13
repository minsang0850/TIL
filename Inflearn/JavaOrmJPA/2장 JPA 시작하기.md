- org.hibernate.hibernate-entitymanager 
  - hibernate-core
  - javax.persistence:javax.persistence-api 
    - jpa 인터페이스

### JPA 설정하기 - persistence.xml
- JPA 설정파일
- /META-INF/persistence.xml 위치
- persistence-unit name으로 지정
- javax.perssistence로 시작: JPA 표준 속성
- hibernate로 시작: 하이버네이트 전용 속성

### 데이터베이스 방언
- JPA는 특정 데이터베이스에 종속x
- 각각의 DB가 제공하는 SQL 문법과 함수는 조금씩 다름
  - 가변 문자: MySQL은 VARCHAR, Oravle은 VARCHAR2
  - 문자열을 자르는 함수: SQL 표준은 SUBSTRING(), Oracle은 SUBSTR()
  - 페이징: MYSQL은 LIMIT, Oracle은 ROWNUM
- 방언: SQL 표준을 지키지 않는 특정 DB만의 고유한 기능

### JPA 구동 방식
- 설정 정보 조회
  - META-INF/persistence.xml
- EntityManagerFactory 생성
- EntityManager 생성

```java
 public static void main(String[] args) {
      EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

      EntityManager em = emf.createEntityManager(); //데이터베이스 커넥션 받은것과 비슷

      EntityTransaction tx = em.getTransaction();

      tx.begin();

      try {
          Member member = em.find(Member.class, 150L);
          member.setName("ZZZZZ");
          tx.commit();
      } catch (Exception e){
          tx.rollback();
      } finally {
          em.close();
      }
      emf.close();
  }
   ```

### 주의
- EntityManagerFactory는 하나만 생성해서 애플리케이션 전체에서 공유 
- EntityManager는 쓰레드간 공유 X 
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행

```java
em.createQuery = em.createQuery("~~")
                   .getResultList();
```


JPA는 SQL을 추상화한 JPQL이라는 객체지향 쿼리 언어 제공

- JPQL은 엔티티 객체를 대상으로 쿼리
- SQL은 데이터베이스 테이블을 대상으로 쿼리


