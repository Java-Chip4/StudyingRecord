# Spring Introduction
[[스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/스프링-입문-스프링부트/)]

---
## 1. 프로젝트 환경설정
- Spring project 생성
- 기본 라이브러리 살펴보기
- index 페이지 생성

## 2. 스프링 웹 개발 기초
### 1) 정적 컨텐츠
### 2) MVC와 템플릿 엔진
- @RequestParam을 사용한 Controller 구성
- Controller와 viewResolver, Template engine 간의 기본적인 상호작용 살펴보기
### 3) API
- @ResponseBody를 사용한 Controller 구성
- @ResponseBody를 사용한 Controller와 HttpMessageConveter 간의 기본적인 상호작용 살펴보기
    - 객체를 반환할 경우, MappingJackson2HttpMessageConveter가 호출되어 사용된다  
      이때, 객체의 멤버변수 접근지정자가 private라면 getter가 있어야만 노출된다

## 3. 회원 관리 예제 - 백엔드 개발
### 1) 비즈니스 요구사항 정리
### 2) 회원 도메인과 리포지토리 만들기
- 요구사항에 맞는 객체 domain 구현
- domain 객체를 관리하기 위한 Repository 구성
    - DBMS가 정해지지 않았다는 가정 하에 repository interface 구성
    - 대안으로 memoryRepository를 구현
### 2) 회원 리포지토리 테스트 케이스 작성
- @AfterEach를 사용한 테스트 케이스 작성
    - [Tip 1] : 순서에 의존적인 설계는 지양하자
    - [Tip 2] : 테스트 하나가 끝나면 항상 데이터를 클리어 시키자
### 3) 회원 서비스 개발
- domain 객체의 핵심 비즈니스 로직 구현
    - [Tip 3] : Service는 비지니스, Repository는 개발 롤에 맞게록 용어를 설계한다
### 4) 회원 서비스 테스트
- [Tip 4] : production code는 한글명을 지양하지만, test code는 직관성을 위해 한글명을 사용할 수 있다
- @BeforeEach를 사용한 테스트 케이스 작성
    - 테스트가 서로 영향이 없도록 새로운 객체와 의존관계를 맺음
    - 이를 위해, 기존 service 코드를 DI 가능하게 변경

## 4. 스프링 빈과 의존관계
### 1) 컴포넌트 스캔과 자동 의존관계 설정
- @Service, @Repository, @Controller를 통한 컴포넌트 스캔과 Spring Bean 등록
    - Spring Bean은 기본적으로 싱글톤으로 등록된다
- @Autowired를 통한 DI
### 2) 자바 코드로 직접 스프링 빈 등록하기
- @Configuration과 @Bean을 통한 Spring Bean 등록
- 세 가지의 DI 방법
    - Field Injection : Application 조립 전 변경할 방법이 없어 지양한다
    - Setter Injection : public으로 노출이 되면, Application 조립 후, 변경 위험성이 있어 지양한다
    - Constructor Injection : Application이 조립된 이후 변경을 막아주기 때문에 안전하다
- [Tip 5] : @Autowired를 통한 DI는 Spring container에 등록된 객체에서만 동작한다

## 5. 회원 관리 예제 - 웹 MVC 개발
### 1) 회원 웹 기능 - 홈 화면 추가
- Controller를 추가하여 홈 화면이 뜨도록 매핑
### 2) 회원 웹 기능 - 등록
- 데이터를 전달 받을 form 객체 구성
- @PostMapping을 사용한 Controller method 구현
### 3) 회원 웹 기능 - 조회
- thymeleaf를 사용한 HTML 페이지 구성

## 6. 스프링 DB 접근 기술
### 1) H2 데이터베이스 설치
- H2 Database 설치 및 테이블 생성
### 2) Jdbc 리포지토리 구현
- JdbcRepository 생성 및 Jdbc API를 통한 SQL 조작 구현
- ❗️느낀점 : memoryRepository를 기반으로 Service를 구현하였기 때문에,
  이를 JdbcRepository로 바꾸어 사용하려면 Service의 코드 변경이 수반될 것이라고 생각했다.
  그러나 그 둘은 repository interface를 구현하여 만들어졌기에 다형성을 이용해 Service의 코드 변경이 필요하지 않았다.
  이번 내용을 통해 실무에 적용가능한 OOP의 강력함, Spring과 DI에 대해 조금이나마 깨닫게 되었다.
### 3) 스프링 통합 테스트
- @SpringBootTest와 @Transactional을 활용한 통합 테스트 실행
    - 🔨 시행착오 : @Transactional이 있으면 테스트 완료 후, 롤백 시키기 때문에 DB 상에서 테스트 데이터를 확인할 수 없다  
      그러므로 테스트 데이터 확인을 위해서는 @Transactional을 제거하거나, 실행할 메소드 혹은 클래스에 @Rollback(false) 혹은 @Commit 을 추가하면 된다
- [Tip 6] : 모든 기능을 쪼개어 단위 테스트가 가능해야 좋은 테스트 설계일 확률이 높다. 통합 테스트보다는 단위 테스트를 지향하자
### 4) 스프링 JdbcTemplate
- JdbcTemplate을 활용한 Repository 생성
### 5) JPA
- JPA를 통한 Repository 생성
- @Id, @GeneratedValue, @Column을 사용한 JPA Entity mapping
- SpringConfig에서 EntityManager는 생성자 DI도 되지만, @PersistenceContext 를 사용하는 것도 가능하다
- JPA를 통한 모든 데이터 변경은 Transaction 안에서 실행해야 한다
- [Tip 7] : PK 기반 서칭이 아닌 메소드는 보통 JPQL 작성이 필요하다
### 6) 스프링 데이터 JPA
- 스프링 데이터 JPA를 통한 Repository interface 구현
- ❗️느낀점 : findBy와 같은 키워드를 통해 원하는 쿼리를 만들어 낼 수 있다는 점이 놀라웠다.  
  이에 대한 자세한 내용이 궁금하여 확인해보니 아래 링크에서 다양한 magic keywords를 확인할 수 있었다.  
  [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.details](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.details)

## 7. AOP
### 1) AOP가 필요한 상황
- 공통 관심 사항(cross-cutting concern)과 핵심 관심 사항(core concern)에 대한 이해
- 둘의 코드가 섞여 유지보수 용이성이 우려되는 경우가 AOP가 필요한 상황이라고 할 수 있다
### 2) AOP 적용
- @Aspect과 @Around를 사용한 AOP 구현
- AOP를 스프링에 등록할 때는 @Component를 사용해도 되지만, AOP 같은 경우 Spring @Bean에 등록하여 사용하는 것을 선호한다.
  그 이유는 AOP는 정형화된 것이 아니기에 인지할 수 있는 것이 더 좋기 때문이다.
    - 🔨 시행착오 : SpringConfig @Bean에 AOP 메소드를 등록한 상태에서, @Around를 활용해 애플리케이션 패키지 하위의 모든 것에 AOP를 적용하였더니, 순환참조 오류가 발생하였다.
      이는 @Around를 통해 SpringConfig의 AOP 메서드도 AOP로 처리하기 때문이다.
      문제를 해결하기 위해서는 @Bean이 아닌 @Component를 사용하거나, @Around의 AOP 대상에서 SpringConfig을 제거함으로써 해결할 수 있었다.
- Spring은 프록시를 통해 AOP가 가능하게 만든다

## 8. 다음으로
### 스프링 핵심 원리 (기본편)
- [https://github.com/Anthologia/spring-core-basic](https://github.com/Anthologia/spring-core-basic)
