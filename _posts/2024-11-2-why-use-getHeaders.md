---
title: 왜 .getHeaders() 를 사용해야할까?
date: 2024-11-2 19:59:30 + 09:00
categories: [backend, springboot]
tags: [kotlin, spring, jwt]
---

## 헤더에서 토큰 가져오기
헤더에서 jwt token 을 가져올때, .getHeader() 를 사용한다.

```java
class TokenExtractor {
    companion object {
        fun extract(
            request: HttpServletRequest,
            type: String
        ): String? {
            val access: String? = request.getHeader(HttpHeaders.AUTHORIZATION)
            access?.substring(type.length)?.trim()
            return access
        }
    }
}
```
이렇게 하면 AUTHORIZATION 헤더에 있는 코드가 문제없이 가져와진다.

하지만, 타 코드들을 보면 `getHeader()` 가 아닌, `getHeaders()` 를 사용한다.

왜 그런것일까?

## getHeaders 사용 이유
getHeaders 를 사용하는 이유는, Authorization 이 여러개 있을 수 있기 때문이다.

### 1. 프록시 게이트웨이
서비스 하는 시스템이 프록시 서버나, API 게이트웨이를 사용하는 경우에는 클라이언트가 Authorization 에 여러개를 포함시켜야 한다.

예를 들어, 클라이언트의 토큰을 검증 후, 추가적인 내부 서비스 요청을 전달할 경우에, 다른 인증 서비스를 위해 새로운 Authorization 헤더를 추가해야한다.

### 2. 다중 인증 요구
가장 대표적인 이유인데, 브라우저에서 다중 인증 Oauth 와 기본 인증을 모두 요구할 경우 시스템에서 여러개의 Authorization 을 사용해야 하기 때문이다.

### 3. 브라우저 인증 문제
브라우저(웹 서버)에서 자체적으로 Authorization 을 넣어주는 경우도 있어, 제대로 된 token 인지 확인하기 위해, getHeaders 를 사용해야 한다.

## getHeaders()로 변경
따라서 getHeaders() 로 변경해 보았다.

```java
class TokenExtractor {
    companion object {
        fun extract(
            request: HttpServletRequest,
            type: String
        ): String? {
            val access: Enumeration<String> = request.getHeaders(HttpHeaders.AUTHORIZATION)
            while (access.hasMoreElements()) {
                val value: String? = access.nextElement()
                if (value?.startsWith(type) == true) {
                    return value.substring(type.length).trim()
                }
            }
            return null
        }
    }
}
```
