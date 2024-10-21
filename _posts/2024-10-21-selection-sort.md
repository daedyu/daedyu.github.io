---
title: 선택정렬 (Selection sort)
date: 2024-10-21 10:45:00 + 09:00
categories: [data-science, algorithm]
tags: [algorithm]
---

## 선택정렬이란?
선택정렬은 리스트의 앞에서부터 값을 가져오고, 해당 값 앞에서부터 다시 순회하여 작은값과 교체하는 정렬방법이다.

1. `값 = 5` --> 앞부터 순회

    | 0 | 1 | 2 |
    |---|---|---|
    | 5 | 3 | 8 |

    3부터 순회 --> 가장 작은 값: 3
    
    5와 3 교환

    | 0 | 1 | 2 |
    |---|---|---|
    | 3 | 5 | 8 |
2. `값 = 5` 

    찾은 값 없음
3. `값 = 8` 

    찾은 값 없음
4. 수행 종료 **(정렬 완료)**

### 시간복잡도

최선 : `O(n²)`

평균 : `O(n²)`

최악 : `O(n²)`

### 안정성

선택정렬은 안정성을 만족하지 않는다.

> [안정성이란?](/posts/what-is-sort) 
{: .prompt-tip }

## 코드 (python)
선택정렬의 코드를 파이썬으로 구현하였다.
```python
# 전체코드
def selection_sort(arr: list):
    n = len(arr)
    for i in range(n - 1):
        least = i
        for j in range(i+1, n):
            if arr[j] < arr[least]:
                least = j
        arr[i], arr[least] = arr[least], arr[i]
```

`def selection_sort(arr: list):` : 배열을 인자로 받는 선택정렬 함수를 정의한다.

`n = len(arr)` : 배열의 길이를 구한다 (인덱스 X)

`for i in range(n - 1)` : 맨 뒤를 제외한 배열 인덱스 만큼 반복한다.

`least = i` : 가장 작은 값을 시작값으로 정한다.

`for j in range(i+1, n)` : 시작값 + 1 부터 반복한다.

`if arr[j] < arr[least]` : 시작값보다 순회값이 작다면

`least = j` : least(최솟값) 을 j 로 바꾼다.

`arr[i], arr[least] = arr[least], arr[i]` : 순서를 교체한다.


> 코드 구현은 쉽지만 시간복잡도가 너무 커 실제로 사용되지 않는다.
{: .prompt-warning }
