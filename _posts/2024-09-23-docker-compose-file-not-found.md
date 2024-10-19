---
title: 도커 컴포즈 설치 오류
date: 2024-09-23 13:30:20 +09:00
categories: [backend]  
author: daedyu
tags: [docker]
---

## 도커설치 트러블 슈팅
docker-compose 를 ec2 ubuntu 에 설치하던 과정에서
```shell
$ docker-compose --version
-bash: /usr/bin/docker-compose: No such file or directory
```
다음과 같은 오류가 발생하였다.

이로 인해 문제를 해결하기 위해 여러 시도를 해보았다.
### 1. 권한 설정 (실패)
```shell
sudo chmod +x /usr/local/bin/docker-compose
```
docker-compose 의 권한 문제일 수도 있을것 같아 다음과 같이 시도해 보았지만 실패하였다.

### 2. 재설치 (실패)
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
docker-compose 재설치를 시도해보았지만 실패하였다.

### 3. 심볼릭 링크 재설정 (성공)
```shell
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
심볼릭 링크를 생성 후 다시 docker-compose --version 을 하니 성공하였다.

이렇게 심볼릭을 통해 문제를 해결하였고 docker-compose 사용을 시작할 수 있게 되었다.
