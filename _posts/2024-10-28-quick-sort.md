---
title: 퀵정렬 (quick sort)
date: 2024-10-28 11:03:50 + 09:00
categories: [data-science, algorithm]
tags: [algorithm, divide-and-conquer]
---

## 퀵 정렬이란?
퀵 정렬은 말 그대로 빠른(quick) 정렬이다.

퀵정렬은 분할-정복기법을 사용하여, 조금씩 잘게 나누어 정렬하는 방법이다.

퀵정렬의 시간복잡도는 `O(nlog₂n)` 을 가진다.

> 최악의 경우(정렬된 상태)에서는 O(n²)을 가진다.
{: .prompt-warning}
### 안정성
정렬의 안정성은 만족하지 않는다.

## 퀵 정렬의 방법

퀵 정렬의 방법은 다음과 같다.
1. 처음 요소를 피봇으로 정하고, 피봇 다음의 요소를 low, 마지막 요소를 high 로 지정한다.
2. low 가 피봇보다 작거나 같으면 오른쪽으로 한칸씩 옮긴다. (크다면 멈춘다.)
3. high 가 피봇보다 크면 왼쪽으로 한칸씩 옮긴다. (작거나 같다면 멈춘다.)
4. 둘다 멈추었을때 
   - 만약 high 의 인덱스 위치가 low 보다 크다면 low, high 를 교체시킨다.
   - 만약 high 의 인덱스 위치가 low 보다 작다면, 피봇과 high 를 교체시킨다.
5. 피봇과 high 가 교체되면, 교체된 인덱스를 기준으로 반을 나누어 재귀로 다시 정렬을 실행 시킨다.

> 5번 과정이 분할 정복 기법을 사용하였다.
{: .prompt-tip}

## 파이썬 코드
```python
def quick_sort(arr, left, right):
  if left < right:
    low = left + 1
    high = right
    pivot = arr[left]
    while low <= high:
      while low <= right and arr[low] <= pivot: low += 1
      while high >= left and arr[high] > pivot: right -= 1
      if low < high: arr[low], arr[high] = arr[high], arr[low]
    arr[left], arr[high] = arr[high], arr[left]
    quick_sort(arr, left, high-1)
    quick_sort(arr, high+1, right) 
```

실행 흐름은 다음과 같다.
1. 함수 선언
2. left 가 right 보다 작다면 아래 코드 실행
3. 기본 변수들을 선언한다.
4. high 가 low 보다 작을때 까지 반복한다.
5. low 가 right 보다 크거나 `arr[low]` 가 pivot 보다 클때까지 low 를 1씩 증가시킨다.
6. high 가 left 보다 작거나 `arr[high]` 가 pivot 보다 작거나 같을때 까지 high 를 1씩 뺀다.
7. i 가 j 보다 작으면 서로를 교체시킨다.
8. 반복을 빠져나온후, 피봇과 high 를 교체시킨다.
9. `0 ~ 피봇-1`, `피봇 + 1 ~ last` 를 기준으로 함수를 실행시킨다.

이렇게 퀵 정렬에 대해 알아보았다.
