---
title: swagger 이미지 업로드 오류 트러블 슈팅
date: 2024-10-20 17:05:10 +09:00
categories: [backend, springboot]
tags: [springboot, troubleshooting]
---

## 문제사항
swagger 에 이미지를 업로드 할 수 있는 명세서를 추가해주었지만, json 객체로 swagger 상에서 테스트 할 수 없게 되었다.

```java
@Operation(summary = "이미지 파일 업로드", description = "이미지 파일을 업로드합니다.")
@PostMapping("/upload")
public ResponseEntity<ImageResponse> uploadImage(
        @Parameter(description = "업로드할 이미지 파일", content = @Content(mediaType = "multipart/form-data"))
        @RequestParam("file") MultipartFile image) throws IOException {
    return ResponseEntity.ok().body(uploadService.upload(image));
}
```
<img src="/assets/img/20241020/noimageupload.png" style="border-radius: 10px">

## RequestPart 사용 (실패)
@RequestPart 를 사용하면 된다라는 글이 있어 시도했지만 실패하였다.

## Content, @Schema 작성 (실패)
@Parameter 에 Content 와 schema 를 추가해주었다.
```java
@PostMapping("/upload")
public ResponseEntity<ImageResponse> uploadImage(
        @Parameter(description = "업로드할 이미지 파일", content = @Content(mediaType = "multipart/form-data", schema = @Schema(type = "string", format = "binary")))
        @RequestPart("file") MultipartFile image) throws IOException {
    return ResponseEntity.ok().body(uploadService.upload(image));
}
```
이렇게 schema 를 지정하고 mediaType 을 지정해주었음에도 작동하지 않았다.

## 버젼 변경 (실패)
혹시나 버젼 이슈인가 확인하였는데,

```gradle
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.0.2'
implementation 'io.springfox:springfox-swagger2:3.0.0'
implementation 'io.springfox:springfox-swagger-ui:3.0.0'
```
springdoc 과 springfox 를 혼합해서 사용중이였다.

spring 3.3.2 를 사용중이였기에 springfox 관련 의존성을 모두 제거하였다.
```gradle
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.0.2'
```

이후에도 확인해보았지만 작동하지 않았다.

## 미디어타입 추가
구현 방식이 다른가 싶어 찾아보다 스프링 3 이후부터 @PostMapping 에 consume = 를 적어 미디어타입을 지정해주어야 한다는 것을 알게 되었다.

```java
@Operation(summary = "이미지 파일 업로드", description = "이미지 파일을 업로드합니다.")
@PostMapping(value = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public ResponseEntity<ImageResponse> uploadImage(
        @RequestParam("file") MultipartFile image) throws IOException {
    return ResponseEntity.ok().body(uploadService.upload(image));
}
```

이후 확인해보니 작동되었다!

<img src="/assets/img/20241020/imageupload.png" style="border-radius: 10px">

> springdoc 를 사용해야 해결가능하다!
{: .prompt-warning }
