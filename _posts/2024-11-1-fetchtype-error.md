---
title: 지연로딩 에러 트러블 슈팅
date: 2024-11-01 17:12:30 + 09:00
categories: [backend, springboot]
tags: [troubleshooting, springboot, jpa, database]
---

## 유저에서 학교/학과 가져오기
기존에 내정보를 가져오는 것과 학과 정보를 가져오는 엔드포인트를 합쳤었다. 하지만, 프론트 측에서 변경을 요청하였다.

따라서 기존 코드를 다음과 같이 수정하였다.

### UserResponse 수정
```java
@Getter
@Builder
public class UserResponse {
    private String email;
    private String nickname;
    private String description;
    private String profileImage;
    private DepartmentResponse department;
  
    public static UserResponse fromUserEntity(UserEntity userEntity) {
        return UserResponse.builder()
                .email(userEntity.getEmail())
                .profileImage(userEntity.getProfileImage())
                .description(userEntity.getDescription())
                .nickname(userEntity.getNickname())
                .department(DepartmentResponse.fromDepartmentEntity(userEntity.getDepartment()))
                .build();
    }
}
```
userResponse fromUserEntity 에서 department 를 추가하였다.

### DepartmentResponse

```java
@Getter
@Builder
public class DepartmentResponse {
    private String name;
    private Integer grade;
    private String school;
    private List<String> subjects;

    public static DepartmentResponse fromDepartmentEntity(DepartmentEntity departmentEntity) {
          return DepartmentResponse.builder()
                  .name(departmentEntity.getName())
                  .grade(departmentEntity.getGrade())
                  .school(getSchool(departmentEntity.getSchoolEntity()).getName())
                  .subjects(departmentEntity.getSubjects())
                  .build();
    }
}
```

이렇게 하고, 실행하니 오류가 생겼다.
> LazyInitializationException: could not initialize proxy [org.example.memoaserver.domain.school.entity.DepartmentEntity#1] - no Session
{: .prompt-danger}

## null 반환 설정 (실패)
오류가 생기자 마자 오류코드를 보는것이 아니라, db부터 확인하는 실수를 하였다.

db를 확인해보니, 테스트 user 의 학과가 null 로 되어있어 null 문제인줄 알았다.

따라서 departmentResponse 코드를 아래와 같이 수정했다.
```java
@Getter
@Builder
public class DepartmentResponse {
    private String name;
    private Integer grade;
    private String school;
    private List<String> subjects;

    public static DepartmentResponse fromDepartmentEntity(DepartmentEntity departmentEntity) {
        if(departmentEntity != null) {
            return DepartmentResponse.builder()
                    .name(departmentEntity.getName())
                    .grade(departmentEntity.getGrade())
                    .school(getSchool(departmentEntity.getSchoolEntity()).getName())
                    .subjects(departmentEntity.getSubjects())
                    .build();
        }

        return null;
    }
}
```
하지만 null 문제가 아니였고, 지속해서 오류가 발생하였다.

## 지연 로딩 문제
이후, 로그를 보니 지연로딩 에러가 일어나고 있었다. 

Hibernate 가 **지연로딩**을 사용하고 있었는데, Department 엔티티의 메서드를 호출할 세션이 닫쳐 db에서 로딩되지 않은 데이터가 생긴 문제였다.

현재 UserEntity 는 다음과 같다.
```java
@Getter @SuperBuilder(toBuilder = true)
@Entity(name = "user")
@NoArgsConstructor
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(unique = true)
    private String nickname;

    private String description;

    @JsonIgnore
    @Enumerated(EnumType.STRING)
    private Role role;

    @JsonIgnore
    private String password;

    @Column(columnDefinition = "TEXT")
    private String profileImage = "https://memoa-s3.s3.ap-northeast-2.amazonaws.com/profile.jpg";

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id", nullable = true)
    private DepartmentEntity department;

    private Date birth;

    private Integer grade;
}
```

이를 다음과 같이 수정하였다.
### 수정된 코드
```java
@Getter @SuperBuilder(toBuilder = true)
@Entity(name = "user")
@NoArgsConstructor
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(unique = true)
    private String nickname;

    private String description;

    @JsonIgnore
    @Enumerated(EnumType.STRING)
    private Role role;

    @JsonIgnore
    private String password;

    @Column(columnDefinition = "TEXT")
    private String profileImage = "https://memoa-s3.s3.ap-northeast-2.amazonaws.com/profile.jpg";

    @ManyToOne(fetch = FetchType.EAGER)
    @JoinColumn(name = "department_id", nullable = true)
    private DepartmentEntity department;

    private Date birth;

    private Integer grade;
}
```

이러니 UserEntity 에서 생기는 오류는 사라졌다.

하지만, DepartmentEntity 에서 같은 오류가 발생했다.

## 지연 로딩 문제 2
따라서 departmentEntity 의 코드를 살펴보았다.

```java
@Entity(name = "department")
@Getter @Setter
@NoArgsConstructor
public class DepartmentEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private Integer grade;

    @ManyToOne
    @JoinColumn(name = "school_entity_id", nullable = false)
    private SchoolEntity schoolEntity;

    @ElementCollection
    private List<String> subjects;
}
```

위와 같이 작성된 코드에서 subjects 가 오류가 발생했다.

### @ElementCollection 의 기본은 LAZY 로딩이였다!

따라서 ElementCollection 의 로딩을 EAGER 로 수정했다.

```java
@Entity(name = "department")
@Getter @Setter
@NoArgsConstructor
public class DepartmentEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private Integer grade;

    @ManyToOne
    @JoinColumn(name = "school_entity_id", nullable = false)
    private SchoolEntity schoolEntity;

    @ElementCollection(fetch = FetchType.EAGER)
    private List<String> subjects;
}
```

이렇게 수정하니, 오류없이 `/me` 에서 학과정보를 볼 수 있게 되었다.

## 느낀점
1. 에러 코드를 잘 확인해야한다는 것을 느끼게 되었다.
