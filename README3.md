# 공통 인터페이스 기능
- 순수 JPA 기반 리포지토리 만들기
- 스프링 데이터 JPA 공통 인터페이스 소개
- 스프링 데이터 JPA 공통 인터페이스 활용

## 순수 JPA 기반 리포지토리 만들기
- 기본 CRUD
  - 저장
  - 변경 -> 변경감지 사용
  - 삭제
  - 전체 조회
  - 단건 조회
  - 카운트

> 참고: JPA에서 수정은 변경감지 기능을 사용하면 된다.  
> 트랜잭션 안에서 엔티티를 조회한 다음에 데이터를 변경하면, 트랜잭션 종료 시점에 변경감지 기능이 작동해서 변경된 엔티티를 감지하고 UPDATE SQL을 실행한다.  

**순수 JPA 기반 리포지토리 - 회원 (MemberJpaRepository)**
```java
package study.jehundatajpa.repository;

import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.springframework.stereotype.Repository;
import study.jehundatajpa.entity.Member;

import java.util.List;
import java.util.Optional;

@Repository
public class MemberJpaRepository {

    @PersistenceContext
    private EntityManager em;

    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    public void delete(Member member) {
        em.remove(member);
    }

    public List<Member> findAll() {
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }

    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    public long count() {
        return em.createQuery("select count(m) from Member m", Long.class)
                .getSingleResult();
    }

    public Member find(Long id) {
        return em.find(Member.class, id);
    }
}
```
- JPA는 변경(수정) 할 때 '변경 감지'라는 기능으로 하기 때문에 update 에 대한 기능이 필요없다.


**순수 JPA 기반 리포지토리 - 팀 (TeamJpaRepository)**
```java
package study.jehundatajpa.repository;

import org.springframework.stereotype.Repository;
import study.jehundatajpa.entity.Team;

import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import java.util.List;
import java.util.Optional;

@Repository
public class TeamJpaRepository {
    
    @PersistenceContext
    private EntityManager em;
    
    public Team save(Team team) {
        em.persist(team);
        return team;
    }
    
    public void delete(Team team) {
        em.remove(team);
    }
    
    public List<Team> findAll() {
        return em.createQuery("select t from Team t", Team.class)
                .getResultList();
    }
    
    public Optional<Team> findById(Long id) {
        Team team = em.find(Team.class, id);
        return Optional.ofNullable(team);
    }
    
    public long count() {
        return em.createQuery("select count(t) from Team t", Long.class)
                .getSingleResult();
    }
    
}
```

- 회원 리포지토리와 거의 동일하다.

**순수 JPA 기반 리포지토리 테스트 (MemberJpaRepository 에 추가)**
```java
import static org.assertj.core.api.Assertions.assertThat;

    @Test
    public void basicCRUD() {

        Member member1 = new Member("member1");
        Member member2 = new Member("member2");
        memberJpaRepository.save(member1);
        memberJpaRepository.save(member2);

        //단건 조회 검증
        Member findMember1 = memberJpaRepository.findById(member1.getId()).get();
        Member findMember2 = memberJpaRepository.findById(member2.getId()).get();
        assertThat(findMember1).isEqualTo(member1);
        assertThat(findMember2).isEqualTo(member2);
        
        //리스트 조회 검증
        List<Member> all = memberJpaRepository.findAll();
        assertThat(all.size()).isEqualTo(2);
        
        //카운트 검증
        long count = memberJpaRepository.count();
        assertThat(count).isEqualTo(2);
        
        //삭제 검증
        memberJpaRepository.delete(member1);
        memberJpaRepository.delete(member2);
        long deletedCount = memberJpaRepository.count();
        assertThat(deletedCount).isEqualTo(0);
    }
```
- 기본 CRUD를 검증한다.

**단건 조회**  
![img.png](image/section3/img.png)  

**리스트 조회**  
![img_1.png](image/section3/img_1.png)

**카운트 검증**  
![img_2.png](image/section3/img_2.png)

**삭제 후 카운트 검증**  
![img_3.png](image/section3/img_3.png)

---