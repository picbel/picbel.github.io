---
sidebar:
nav: "main"
layout: single
title:  "JPA - 영속성 컨텍스트"
categories: [Java,Spring Data Jpa,JPA]
tags: [Java,Spring Data,Spring Data Jpa,JPA]
teaser: /assets/images/teaser/jpa.png
---

<p align="center"><img src="/assets/images/teaser/jpa.png" width="100%" height="auto"></p>

JPA에서 알아야 할 가장 기초인 영속성 컨텍스트에 대한 포스트입니다.

영속성 컨텍스트란?
---
__엔티티를 영구 저장하는 환경__ 애플리케이션과 DB사이에 위치한다.\
영속성 컨텍스트는 논리적인 개념이다. 엔티티 매니저를 통해 접근 할 수 있다.
> EntityManager.persist(entity);

위와 같이 엔티티 매니저를 통해 엔티티를 저장하려 하면 DB에 바로 인서트 하는 게 아닌 영속성 컨텍스트에서 영속화한다는 뜻이다.\
저장은 그 이후의 일이다.

__앤티티 매니저 생성하는 방법__
- EntityManagerFactory
  - 애플리케이션에서 한 번만 생성해서 공유하도록 설계되어있다.
  - 동시 성 문제에서 안정성이 보장된다.

- EntityManager
  - 동시 성 문제에서 안정성 보장이 되지 않는다.
  - 여러 스레드에서 공유 금지
  > 스프링에서 @PersistenceContext를 사용하여 EntityManager를 주입받으면 동시성 문제를 해결하여준다. (Thread-safe 보장)\
  > EntityManager를 프록시객체를 주입후EntityManager가 호출될 때마다 생성하여 해결한다.


앤티티의 생명주기
---
<p align="center"><img src="/assets/images/posts/20220118/entity2.png" width="100%" height="auto"></p>

- ## 비영속 (new/transient)
  - 영속성 컨텍스트와 관계가 없는 상태
    ```java
    Member member = new Member();
    member.setId("member1")
    member.setUsernmae("새로운 회원");
    ```

- ## 영속 (managed)
  - 영속성 컨텍스트에 관리되어 있는 상태
    ```java
     Member member = new Member();
     member.setId("member1")
     member.setUsernmae("새로운 회원");
    
     EntityManager em = emf.createEntityManager();
     em.getTransaction().begin();
  
     //객체를 영속화
     em.persist(member);
    ```

- ## 준영속 (detached)
  - 영속성 컨텍스트에 저장되었다가 *분리*된 상태
    ```java
    em.detach(member);
    ``` 

- ## 삭제 (removed)
  - 영속성 컨텍스트에서 삭제된 상태
    ```java
    em.remove(member);
    ``` 

영속성 컨텍스트의 장점 및 기능
---
- 1차 캐시
  - 영속성컨텍스트에는 내부에 1차 캐시가 존재한다.
  - 1차 캐시는 HashMap구조이며 key는 id로 선언한 필드 값, value는 해당 앤티티이다.
  - 잦은 조회나 영속성에 이미 정보가 있으면 굳이 DB에 가서 찾지 않는다.(주의해야 하는 부분)
    즉 반복 가능한 읽기(REPEATABLE READ) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션 차원에서 제공한다.
  - 1차 캐시는 모든 스레드가 공유하는 게 아닌 스레드 하나에서만 쓴다.


- 동일성 보장
  - 1차 캐시에서 오는 두 번째 장점이다.
  - 1차 캐시를 통해 조회하기 때문에 같은 메모리 주소의 객체를 가져온다.


- 트랜잭션 지원 쓰기 지연, 변경 감지, 지연 로딩
  - 트랜잭션에서 인서트가 발생하면 **쓰기 지연 SQL 저장소**에 SQL을 작성하여 저장한다.\
    실제 트랜잭션이 커밋이 될 때 DB에 insert 한다.
  
  - 영속화되어 관리되는 엔티티의 경우 1차 캐시에서 관리 당시 내부의 엔티티에 대한 스냅숏과 비교하여 변경된 정보를 update한다.


플러시 (flush)
---
영속성 컨텍스트의 변경 내용을 데이터베이스에 반영하는 기능\
SQL저장소에 있는 쿼리를 실제 DB에 보낸다.


### 영속성 컨텍스트를 플러시 하는 법
- em.flush() - 직접 호출
- 트랜잭션 커밋 - 플러시 자동 호출
- JPQL 쿼리 실행 - 플러시 자동 호출

### 주의 할 점 !
- 영속성 컨텍스트를 비우지 않는다. 내보낸다는 느낌보단 동기화라는 개념이 더 적합하다,
- 트랜잭션이라는 작업 단위가 중요하다. 


  



