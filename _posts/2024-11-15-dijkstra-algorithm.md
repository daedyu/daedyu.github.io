---
title: Dijkstra 알고리즘
date: 2024-11-15 10:41:30 +09:00
categories: [data-science, algorithm]
tags: [algorithm, dijkstra, shortest-paths]
---

## Dijkstra 알고리즘이란?
dijkstra 알고리즘은 그래프의 한 정점에서 다른 경로의 최단경로의 길이를 구하는 알고리즘이다.

> 하나의 정점에서 다른정점의 거리를 구할때 사용한다.

## 코드흐름
### 1. 그래프 정의
1. 그래프를 딕셔너리 배열로 선언한다.
```python
graph = {
    'A': {'B': 1, 'C': 4},
    'B': {'A': 1, 'C': 2, 'D': 5},
    'C': {'A': 4, 'B': 2, 'D': 1},
    'D': {'B': 5, 'C': 1}
}
```
2. 최단거리 리스트를 만든다. (기본을 inf 로 정의한다.)
```python
distances = {node: float('inf') for node in graph}
```
3. 우선순위 큐를 만든다. (시작 정점을 추가해놓는다.)
```python
distances[start] = 0
priority_queue = [(0, start)] 
```

### 2. 경로 탐색
```python
while priority_queue:
    current_distance, current_node = heapq.heappop(priority_queue)
    if current_distance > distances[current_node]:
        continue
```
- 우선순위 큐에서 가장 짧은 거리의 정점을 꺼내고 정점을 설정한다.
- 현재 노드의 최단 거리가 이미 계산된 거리보다 크면, 해당 정점는 무시하고 다음 정점을 큐에서 꺼낸다.

### 3. 인접정점 거리 갱신
```python
for neighbor, weight in graph[current_node].items():
    distance = current_distance + weight
    if distance < distances[neighbor]:
        distances[neighbor] = distance
        heapq.heappush(priority_queue, (distance, neighbor))
```
- 최단거리가 갱신된 새 정점의 인접 노드들을 큐에 넣는다.

### 4. 결과 반환
```python
return distances
```

- 큐가 비게 되면 정점을 반환한다.

### 전체 흐름
1.	그래프를 초기화하고 각 노드의 최단 거리를 무한대로 설정.
2.	시작 노드의 거리를 0으로 설정하고 우선순위 큐에 추가.
3.	큐에서 현재 노드를 꺼내고, 해당 노드의 최단 경로를 찾았다면 갱신하지 않음.
4.	현재 노드의 모든 인접 노드를 검사하며, 짧은 경로가 발견될 때마다 거리 갱신 및 큐에 추가.
5.	큐가 빌 때까지 반복하고, 모든 노드의 최단 경로가 구해지면 결과 반환.

## 전체 코드
```python
import heapq

def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    priority_queue = [(0, start)]

    while priority_queue:
        current_distance, current_node = heapq.heappop(priority_queue)
        if current_distance > distances[current_node]:
            continue

        for neighbor, weight in graph[current_node].items():
            distance = current_distance + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(priority_queue, (distance, neighbor))

    return distances

# Example graph
graph = {
    'A': {'B': 1, 'C': 4},
    'B': {'A': 1, 'C': 2, 'D': 5},
    'C': {'A': 4, 'B': 2, 'D': 1},
    'D': {'B': 5, 'C': 1}
}

start_node = 'A'
shortest_distances = dijkstra(graph, start_node)
print("A로부터의 최단 거리", start_node, ":", shortest_distances)
```
