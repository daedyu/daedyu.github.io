---
title: jwt 유저 가져오기 문제 트러블 슈팅
date: 2024-10-30 20:43:30 + 09:00
categories: [backend, springboot]
tags: [jwt, springboot, troubleshooting]
---

## jwt 유저 가져오기 트러블 슈팅
patch 로 나의 정보를 수정하면서 오류가 발생하였다.
> org.hibernate.HibernateException: identifier of an instance was altered from
{: .prompt-error}

```java
@Component
public class UserAuthHolder {
    public UserEntity current(){
        return ((CustomUserDetails) SecurityContextHolder.getContext().getAuthentication().getPrincipal()).userEntity();
    }
}
```

UserService
```java
public UserResponse updateMe(UpdateUserRequest updateUser) {
    UserEntity userEntity = userAuthHolder.current();

    var toBuilder = userEntity.toBuilder();
    if (updateUser.getNickname() != null) {
        toBuilder.nickname(updateUser.getNickname());
    }

    if (updateUser.getProfileImage() != null) {
        toBuilder.profileImage(updateUser.getProfileImage());
    }

    if (updateUser.getDescription() != null) {
        toBuilder.description(updateUser.getDescription());
    }

    if (updateUser.getPassword() != null && updateUser.getPastPassword() != null) {
        if (bCryptPasswordEncoder.matches(updateUser.getPastPassword(), userEntity.getPassword())) {
            toBuilder.password(bCryptPasswordEncoder.encode(updateUser.getPassword()));
        }
    }
    UserEntity updatedUser = toBuilder.build();
    userRepository.save(updatedUser);

    return UserResponse.fromUserEntity(updatedUser);
}
```

알아보니 jwt 에서 가져온 나의 정보를 수정후 update 하니 오류가 생겼다.

## repository 에서 가져오기
그래서 jwt에 존재하는 email 을 가져온 후 userRepository 에서 searchByEmail 을 통해 유저를 가져왔다.

그렇게 하니 수정은 가능했지만...

### sql 이 두번 요청되었다!

따라서 다른 방법을 모색하였다.

## JwtFilter 수정 (해결)
```java
String email = jwtUtil.getEmail(accessToken);
    Role role = Role.valueOf(jwtUtil.getRole(accessToken));

    CustomUserDetails customUserDetails = new CustomUserDetails(UserEntity.builder()
            .email(email)
            .role(role)
            .build());

    Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());
    SecurityContextHolder.getContext().setAuthentication(authToken);

    filterChain.doFilter(req, res);
```

JwtFilter를 보니 user를 가져오고 다시 builder 를 통해 생성하는 것이였다.

이로인해 entity 생성으로 자동으로 id 가 증가되어 pk 는 변경할 수 었다는 오류가 발생한 것이였다.

jwtFilter
```java
String email = jwtUtil.getEmail(accessToken);

    UserEntity user = userRepository.findByEmail(email);

    CustomUserDetails customUserDetails = new CustomUserDetails(user);

    Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());
    SecurityContextHolder.getContext().setAuthentication(authToken);

    filterChain.doFilter(req, res);
```

userService
```java
public UserResponse updateMe(UpdateUserRequest updateUser) {
    UserEntity userEntity = userAuthHolder.current();

    var toBuilder = userEntity.toBuilder();
    if (updateUser.getNickname() != null) {
        toBuilder.nickname(updateUser.getNickname());
    }

    if (updateUser.getProfileImage() != null) {
        toBuilder.profileImage(updateUser.getProfileImage());
    }

    if (updateUser.getDescription() != null) {
        toBuilder.description(updateUser.getDescription());
    }

    if (updateUser.getPassword() != null && updateUser.getPastPassword() != null) {
        if (bCryptPasswordEncoder.matches(updateUser.getPastPassword(), userEntity.getPassword())) {
            toBuilder.password(bCryptPasswordEncoder.encode(updateUser.getPassword()));
        }
    }
    UserEntity updatedUser = toBuilder.build();
    userRepository.save(updatedUser);

    return UserResponse.fromUserEntity(updatedUser);
}
```

다음과 같이 코드를 수정하여 문제를 해결하였다.
