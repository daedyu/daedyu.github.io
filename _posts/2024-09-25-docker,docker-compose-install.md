---
title: 도커, 도커 컴포즈 ubuntu 에 설치하기
date: 2024-09-25 14:00:00 +09:00
categories: [backend, docker]
tags: [docker, guide]
---

## 기본 설정
aws ec2 환경을 구축하였고, ssh 에 접속 한 후 docker 와 docker-compose 를 설치하는 과정이다.

## HTTP 설치
docker 와 docker-compose 를 설치하기 위해 http 패키지를 설치한다.
```shell
sudo apt update
sudo apt-get install -y ca-certificates \ 
    curl \
    software-properties-common \
    apt-transport-https \
    gnupg \
    lsb-release
```
저장소와 gpg 키 또한 설치한다.
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```shell
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## Docker 설치
도커를 설치한다.
```shell
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

> 권한을 설정하지 않고 docker --version 를 하면 작동되지 않습니다.
{: .prompt-tip }

도커가 설치가 되었다면 도커의 권한을 설정한다.

```shell
sudo usermod -aG docker $USER

docker --version
```
버전이 정상적으로 뜨면 성공이다.

## docker-compose
도커 컴포즈를 설치한다.
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
이후 컴포즈의 권한을 설정한다.
```shell
sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```
버전이 정상적으로 뜨면 성공이다.

<a href="/posts/docker-compose-file-not-found">버전 안뜸 해결법</a>
