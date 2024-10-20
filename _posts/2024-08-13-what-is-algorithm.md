---
title: 알고리즘이란
date: 2024-08-13 11:40:00 + 09:00
categories: [algorithm]
tags: [algorithm]
---

## 알고리즘이란?
알고리즘은 컴퓨터가 명령을 수행하며 해결해 나가는 과정이다.

다른말로 문제를 해결하는 명령의 유한 집합이다.

### 알고리즘의 정의
알고리즘은 아래 5가지를 만족해야한다.
1) 입력: 입력 데이터가 **0**개 이상 존재한다.
2) 출력: 알고리즘 수행 후 한가지 이상의 수행 결과가 생성된다.
3) 명확성: 각 명령어가 명확하고, 모호하지 않다.
4) 유한성: 알고리즘의 실행은 유한하다.
5) 유효성: 각 명령어는 실행 가능한 연산이여야 한다. (오류x)
<div style="background-color: rgb(252,237,222); padding: 15px; border-radius: 5px; margin-bottom: 18px">
  <b style="color: #e76d17">Imp</b>
  <span style="padding-left: 5px">하나라도 충족하지 않으면 알고리즘이 아니다!</span>
</div>

## 알고리즘 성능 분석법
### 시간 복잡도
- 알고리즘이 수행하는 작업의 양
- 계산 단계
  - 지정문 `(a: int = 1;)`
  - 산술식 `a = b + c`
  - 반환문 `return a`
  - 함수호출 `print("hello world")`
  - 분기문 `if`
- 단위는 스텝(step) 으로 나타낸다.

```python
a = 2                       # 지정문 (1 step)
a = 2*b+3*c/d-e+f/g/a/b/c   # 산술식 (1 step)
print(3)                    # 함수호출 (1 step)
if a == 1 :                 # 분기문 (1 step)
return 6                    # 반환문 (1 step)
```
총 **5 step** 을 가지는 알고리즘이다.

#### 반복문
반복문은 반복 횟수에 따라 시간 복잡도가 계산된다.
```python
for i in range(5):          # (5 step)
for i in range(n):          # (n step)
```

## 시간 복잡도 표기법들
시간 복잡도를 표현하는 방법은 여러 방식이 있다.

### 빅오(big-O) 표기법
가장 많이 사용하는 표기법이다.

**최악의 상황을 고려**하는 표기법으로, 알고리즘의 수행이 big-O 로 나타내었을 때 더 안좋은 시간 복잡도를 가질 수는 없다.

표기는 O() 안에 스텝의 최고 차수를 적으면 된다.

ex: `a + 1` O(1), `for i in range(n)` O(n)

<div style="background-color: #e0f6f7; padding: 15px; border-radius: 5px; margin-bottom: 18px">
  <b style="color: #29beb9">Tip</b>
  <span style="padding-left: 5px">O(1) ≺ O(log n) ≺ O(n) ≺ O(n log n) ≺ O(n2)
≺ O(n3) ≺ O(2n) ≺ O(3n) ≺ O(n!)</span>
</div>

### 빅오메가 표기법
빅오와 반대로 **최선의 상황을 고려**하는 표기법으로, 빅 오메가 보다 더 좋은 시간 복잡도는 나오지 않는다.

표기는 Ω() 안에 스텝의 최소 차수를 적으면 된다.

### 빅세타 표기법
빅오와 빅세타의 중간 상황을 나타내는 표기봅으로, 더 짧은 시간이 소요될 수도 있고, 더 긴 시간이 소요될 수도 있다.

표기는 빅오의 시간과 빅오메가의 시간이 같을때 사용한다.

근사치를 나타낼때 주로 사용한다.
