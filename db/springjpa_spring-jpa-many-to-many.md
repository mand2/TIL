# Spring JPA 다대다 설정 및 성능 주의 ( Many To Many )

출처: taehyun\_kim 님의 [블로그](https://happyer16.tistory.com/entry/Spring-JPA-%EB%8B%A4%EB%8C%80%EB%8B%A4-%EC%84%A4%EC%A0%95-%EB%B0%8F-%EC%A3%BC%EC%9D%98-Many-To-Many?category=744312)

\
\


### 소개

복잡한 비즈니스 요구 사항이 들어오게 되면 다대다 관계로 모델링을 진행하여야 한다. 이때 어떻게 해야하는지 주의해야 할 점에 대해 정리해보고 싶어 해당 글을 쓰게 되었다.

### Many to Many

학생이 좋아하는 수업을 정의할 때 다대다 관계가 필요해진다. 여러명의 학생이 수업을 좋아할수도 있고, 한 학생이 여러 수업을 좋아할 수도 있다.

![img](https://k.kakaocdn.net/dn/CTGnP/btqz4LBPTyF/RZqkmDDG2C7hWm9wksKwCK/img.png)

이걸 RDB로 표현을 하면 아래와 같은 다이어그램이다.

![img](https://k.kakaocdn.net/dn/bTLb1C/btqz3m3URia/Rv9bbGwLfga5VIC1IT9JF0/img.png)

JPA 코드로 표현을 하면 아래와 같다.

```java
@Entity
class Student {
 
    @Id
    Long id;
 
    @ManyToMany
    @JoinTable(
      name = "course_like", 
      joinColumns = @JoinColumn(name = "student_id"), 
      inverseJoinColumns = @JoinColumn(name = "course_id"))
    Set<Course> likedCourses;
 
    // additional properties
    // standard constructors, getters, and setters
}
 
@Entity
class Course {
 
    @Id
    Long id;
 
    @ManyToMany(mappedBy = "likedCourses")
    Set<Student> likes;
 
    // additional properties
    // standard constructors, getters, and setters
}
```

관계의 소유를 Student로 하고 @JoinTable 어노테이션을 이용하여 관계를 설정할 수 있다.

### Composite Key 만들기

위와 같이 설정을 하게 되면, 한가지 문제가 발생한다. Entity끼리 직접 보고 있기 때문에 관계에 property를 추가하지 못한다. 관계를 나타내는 엔티티클래스를 별도록 만들어야지 필드를 추가할 수 있다.

```
@Entity
class CourseRating {
 
    @EmbeddedId
    CourseRatingKey id;
 
    @ManyToOne
    @MapsId("student_id")
    @JoinColumn(name = "student_id")
    Student student;
 
    @ManyToOne
    @MapsId("course_id")
    @JoinColumn(name = "course_id")
    Course course;
 
    int rating;
     
    // standard constructors, getters, and setters
}

@Embeddable
class CourseRatingKey implements Serializable {
 
    @Column(name = "student_id")
    Long studentId;
 
    @Column(name = "course_id")
    Long courseId;
 
    // standard constructors, getters, and setters
    // hashcode and equals implementation
}
```

@MapsId 는 Many-to-One에서 외래키를 지정할 때 사용된다.

위처럼 관계 엔티티를 별도로 생성했다면 아래와 같이 코드가 바뀐다.

```
class Student {
 
    // ...
 
    @OneToMany(mappedBy = "student")
    Set<CourseRating> ratings;
 
    // ...
}
 
class Course {
 
    // ...
 
    @OneToMany(mappedBy = "course")
    Set<CourseRating> ratings;
 
    // ...
}
```

학생이 등록한 수업에 평점을 주는 것을 개발해보자. 재수강까지 고려해야 한다면 둘의 관계는 다대다가 되어야 한다.

![img](https://k.kakaocdn.net/dn/UyhAI/btqz4LWar00/kU6uv0AQRKASSBCPavn69K/img.png)

```
@Entity
class CourseRegistration {
 
    @Id
    Long id;
 
    @ManyToOne
    @JoinColumn(name = "student_id")
    Student student;
 
    @ManyToOne
    @JoinColumn(name = "course_id")
    Course course;
 
    LocalDateTime registeredAt;
 
    int grade;
     
    // additional properties
    // standard constructors, getters, and setters
}

class Student {
 
    // ...
 
    @OneToMany(mappedBy = "student")
    Set<CourseRegistration> registrations;
 
    // ...
}
 
class Course {
 
    // ...
 
    @OneToMany(mappedBy = "courses")
    Set<CourseRegistration> registrations;
 
    // ...
}
```

### 다대다 설정에서 주의해야 할 점 - JPA 책보고 따라하면 끝?

아래의 코드를 보면 JPA 관점에서는 잘 짠 코드이다. 하지만 그렇다고 항상 효율적인 것은 아니다.

```java
@Entity(name = "Post")
@Table(name = "post")
public class Post {
 
    @Id
    @GeneratedValue
    private Long id;
 
    private String title;
 
    public Post() {}
 
    public Post(String title) {
        this.title = title;
    }
 
    @ManyToMany(cascade = {
        CascadeType.PERSIST,
        CascadeType.MERGE
    })
    @JoinTable(name = "post_tag",
        joinColumns = @JoinColumn(name = "post_id"),
        inverseJoinColumns = @JoinColumn(name = "tag_id")
    )
    private List<Tag> tags = new ArrayList<>();
 
    //Getters and setters ommitted for brevity
 
    public void addTag(Tag tag) {
        tags.add(tag);
        tag.getPosts().add(this);
    }
 
    public void removeTag(Tag tag) {
        tags.remove(tag);
        tag.getPosts().remove(this);
    }
 
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Post)) return false;
        return id != null && id.equals(((Post) o).getId());
    }
 
    @Override
    public int hashCode() {
        return 31;
    }
}
 
@Entity(name = "Tag")
@Table(name = "tag")
public class Tag {
 
    @Id
    @GeneratedValue
    private Long id;
 
    @NaturalId
    private String name;
 
    @ManyToMany(mappedBy = "tags")
    private List<Post> posts = new ArrayList<>();
 
    public Tag() {}
 
    public Tag(String name) {
        this.name = name;
    }
 
    //Getters and setters ommitted for brevity
 
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Tag tag = (Tag) o;
        return Objects.equals(name, tag.name);
    }
 
    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}

final Long postId = doInJPA(entityManager -> {
    Post post1 = new Post("JPA with Hibernate");
    Post post2 = new Post("Native Hibernate");
 
    Tag tag1 = new Tag("Java");
    Tag tag2 = new Tag("Hibernate");
 
    post1.addTag(tag1);
    post1.addTag(tag2);
 
    post2.addTag(tag1);
 
    entityManager.persist(post1);
    entityManager.persist(post2);
 
    return post1.id;
});

doInJPA(entityManager -> {
    Tag tag1 = new Tag("Java");
    Post post1 = entityManager.find(Post.class, postId);
    post1.removeTag(tag1);
});
```

위 코드를 동작시키면

```
SELECT p.id AS id1_0_0_,
       t.id AS id1_2_1_,
       p.title AS title2_0_0_,
       t.name AS name2_2_1_,
       pt.post_id AS post_id1_1_0__,
       pt.tag_id AS tag_id2_1_0__
FROM   post p
INNER JOIN
       post_tag pt
ON     p.id = pt.post_id
INNER JOIN
       tag t
ON     pt.tag_id = t.id
WHERE  p.id = 1
 
DELETE FROM post_tag
WHERE  post_id = 1
 
INSERT INTO post_tag
       ( post_id, tag_id )
VALUES ( 1, 3 )
```

Hibernate 가 post\_tag의 모든 row를 지운다음에 나머지 것들을 다시 insert하는 형태이다. 이는 데이터베이스에 부하를 가하는 행위이며 효율적이지 못하다. 그래서 ManyToMany에서는 List 보다는 Set을 사용하는 것을 추천한다.

### 다대다 매핑일 때는 Set 을 사용하라

List 대신 Set을 사용하면 쿼리가 아래처럼 생성된다.

```
SELECT p.id AS id1_0_0_,
       t.id AS id1_2_1_,
       p.title AS title2_0_0_,
       t.name AS name2_2_1_,
       pt.post_id AS post_id1_1_0__,
       pt.tag_id AS tag_id2_1_0__
FROM   post p
INNER JOIN
       post_tag pt
ON     p.id = pt.post_id
INNER JOIN
       tag t
ON     pt.tag_id = t.id
WHERE  p.id = 1
 
DELETE FROM post_tag
WHERE  post_id = 1 AND tag_id = 3
```
