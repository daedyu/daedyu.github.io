---
title: 필터에서 생긴 에러를 처리하는법
date: 2024-10-23 23:28:00 +09:00
categories: [backend, springboot]
tags: [jwt, filter, error-handling]
---

## Jwt 만료 에러
spring security 에서 jwt를 검증하려면 필터를 이용한다.

하지만, 필터를 사용하면 `@RestControllerAdvice`를 이용하지 못한다.

> 필터에서 일어난 에러는 ControllerAdvice 까지 도달하지 않고, 클라이언트로 반환하기 떄문
{: .prompt-warning}

과연 어떻게 해야 filter 에서 일어난 에러를 처리할 수 있을까?

## 필터를 통해 처리
filter 에서 일어난 오류이기에, filter를 통해 처리하면 어떨까? 라는 생각을 하였다.

```java
public class JwtExceptionHandlerFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain) throws ServletException, IOException {
        try{
            filterChain.doFilter(request, response);
        } catch (JwtException | IllegalArgumentException e) {
            setErrorResponse(response, "토큰 관련 오류", e.getMessage());
        }
    }
    private void setErrorResponse(
            HttpServletResponse response,
            String errorMessage,
            String details
            ) {
        ObjectMapper objectMapper = new ObjectMapper();

        response.setStatus(HttpServletResponse.SC_FORBIDDEN);
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");

        try{
            response.getWriter().write(objectMapper.writeValueAsString(getErrorResponse(errorMessage, details)));
        }catch (IOException e){
            e.printStackTrace();
        }
    }

    private ErrorResponse getErrorResponse(String errorMessage, String details) {
        return ErrorResponse.builder()
                .message(errorMessage)
                .details(details)
                .build();
    }
}
```
```java
http
    .addFilterBefore(new JwtExceptionHandlerFilter(), UsernamePasswordAuthenticationFilter.class)
    .addFilterBefore(new JwtFilter(jwtUtil), LoginFilter.class)
    .addFilterAt(loginFilter, UsernamePasswordAuthenticationFilter.class);

```
doFilter 를 통해 다음 filter 를 호출 후, 오류가 나면 try-catch 를 통해 에러를 처리하게 만들었다.

이렇게 filter 를 통해 filter 에서 생긴 오류를 해결했다.

> jwt 에러처리 말고 필터에서 생긴 다른 에러를 처리하려면, catch 문을 여러개 만들어 사용하면 된다.
{: .prompt-tip}
