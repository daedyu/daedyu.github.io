---
title: DML 문법 (4) - 서브 쿼리
date: 2024-12-02 12:09 +09:00
categories: [backend, database]
tags: [mysql, sql-script]
---

## 서브 쿼리
where 문이나 다른 곳에서 다시 select 구문을 실행시키는것이다.

join 과 subquery 는 속도차이는 없다.

## 단점
서브 쿼리는 인덱스가 생성되지 않기에, 많은 서브 쿼리를 중첩해서 사용하면 효율이 떨어진다.

```sql
select * from customer `c`
where exist (
    select * from user `u`
    where u.name = c.name
  )
;
```

이렇게 서브쿼리는 `()` 안에 작성하면 된다.

## in
서브 쿼리로 반환한 테이블 내에 요소가 있는지 확인하고 싶을때 사용한다.
```sql
select * from customer `c`
where c.name in (
    select name from user `u`
    )
;
```
> 서브쿼리의 반환 값은 in 할 값의 도메인과 일치해야한다. (* 안됨)
{: .prompt-warning}

## exist
in 과 같지만 앞에 검사할 속성을 적지 않아도 된다.
```sql
select * from customer `c`
where exist (
    select * from user `u`
    where u.name = c.name
  )
;
```

## not
포함되지 않는 것을 확인하려면 in & exist 앞에 not 을 붙이면 된다.

이렇게 sub query 에 대해 알아보았다.
