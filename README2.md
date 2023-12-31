# 예제 도메인 모델

## 예제 도메인 모델과 동작 확인

**엔티티 클래스**  
![img.png](image/section2/img.png)

**ERD**  
![img_1.png](image/section2/img_1.png)

핵심이 되는 예제 도메인을 만든다.  
아주 간단한 Member, Team을 만들었다.  
여러 Member가 한 Team에 들어갈 수 있고, Team은 여러 Member를 가지기 때문에 일대다 관계이다.  

**Member 엔티티**
```java

```

**Team 엔티티**
```java
import jakarta.persistence.*;
import lombok.*;

import java.util.ArrayList;
import java.util.List;

@Entity
@Getter @Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "name"})
public class Team {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "team_id")
    private Long id;
    private String name;

    @OneToMany(mappedBy = "team") // 다대일 관계 foreign key가 없는 쪽에 mappedBy를 적는 것이 좋다.
    private List<Member> members = new ArrayList<>();

    public Team(String name) {
        this.name = name;
    }
}
```