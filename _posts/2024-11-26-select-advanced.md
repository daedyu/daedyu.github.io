---
title: DML 문법 (2) - select 문 심화
date: 2024-11-26 11:25 +09:00
categories: [backend, database]
tags: [mysql, sql-script]
---

## select 문 심화
select 문에서 여러 조건들을 만들어 검색할 수 있는 기능을 만들 수 있다.

## like
문자열을 검색하면서, = 를 사용하면 공백까지 일치하는 문자여야 한다.

하지만 like 구문을 사용하여 문자열의 규칙을 정해 탐색할 수 있다.

예시로 성이 김씨인 사람을 찾아보자.

```sql
select name from customer
    where name like '김__'
;
```
이렇게 하면 김으로 시작하는 3글자 이름의 사람을 찾아준다.

'김__' 을 해석하면 `김으로 시작하는 문자(김) 뒤에 두글자(__)`

> [_ 가 어떻게 한국어 문자 하나를 인식할 수 있을까?](/posts/like-principle/)
{: .prompt-info}

하지만 이러면 3글자인 김씨만 찾을 수 있다.

3자 이상의 이름을 찾으려면 어떻게 해야할까?

```sql
select name from customer
    where name like '김%'
;
```
이렇게 `%` 를 사용하면 된다.

'김%'를 해석하면 `김으로 시작하는 ~ 문자열`

`%김%` 으로 하면 `문자 중간에 김이 포함된` 이라는 뜻이 되어,
박김규 와 같은 이름이 있으면 찾아진다.

## null 찾기
값에서 null 인 값이 있을 수 있다.

이를 제외하거나, null 인 값만 가져오고 싶으면 어떻게 해야할까?

`is null` 과 `is not null` 을 사용하면 된다.

> null 인 값은 `=` 를 사용할 수 없다! 
{: .prompt-warning}

예시로 age 가 null 인 사람을 찾아보자.

```sql
select * from customer
    where age is null
;
```

## 정렬하기
데이터를 정렬해서 받고 싶으면 어떻게 해야할까?

데이터를 정렬하는 코드는 다음과 같다.
```sql
select * from customer `c`
    order by c.name asc
;
```
이렇게 하면 정렬이 된다.

### asc
asc 는 데이터를 오름차순으로 정렬하는 방법으로, 생략이 가능하다.(order by 의 기본이 asc 다)

### desc
desc 는 데이터를 내림차순 정렬하는 방법이다.

예시로 고객 데이터를 나이를 기준으로 내림차순 정렬하자.

```sql
select * from customer
    order by age desc
;
```

## 집계함수
데이터를 세거나, 평균을 내거나, 합을 구하고 싶으면 집계함수를 사용하면 된다.

### 집계함수의 종류
- avg() 평균을 내는 함수다.
- sum() 합을 구하는 함수다.
- count() 갯수를 세는 함수다.
- min() 최솟값을 구하는 함수다.
- max() 최댓값을 구하는 함수다.

예로 고객의 나이의 합을 구하고 싶다면 다음과 같이 사용하면 된다.
```sql
select sum(age) from customer;
```

### group by
위와 같이 사용하면 집계함수를 테이블 전체 값에서 계산한다.

하지만, 특정 아이디나 이름마다 다른 값을 도출하고 싶다면 어떻게 해야할까?

```sql
select avg(id) from customer
    group by age;
```

이렇게 하면 나이가 같은 고객의 아이디의 평균값을 출력해준다.

### having
집계함수는 where 구문에 들어갈 수 없다.

where 구문 안에 집계함수를 넣으려면 having 문을 사용하면 된다.

```sql
select sum(age) `sum_age` from customer
    group by name
    having sum_age > 10
;
```

이렇게 having 구문을 사용하면 된다.
