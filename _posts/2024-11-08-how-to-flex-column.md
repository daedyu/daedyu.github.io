---
title: flutter column 위쪽 채우기
date: 2024-11-08 22:18:30 + 09:00
categories: [frontend, flutter]
tags: [flutter, desktop, troubleshooting]
---

## 문제 상황
위쪽 column 의 길이를 길게 하고 아래를 작게 만들고 싶었다.

따라서, column에 길이를 지정했지만, 크기를 조절하면 오버플로우 에러가 발생했다.

## Expended 사용 (해결)
기존 코드
```dart
return Scaffold(
  body: Column(
    children: [
      Row(
        children: [
          Container(
            width: 200,
            child: Text("data"),
          ),
          Expanded(
            child: Text("data"),
          )
        ],
      ),
      Text("data")
    ],
  ),
);
```
위와 같이 row 가 더 높은 크기를 가져야 하지만, row와 텍스트가 상단에 붙어있는 형태가 된다.

> 만약 Expended 로 row 를 묶으면 어떻게 될까?
{: .prompt-tip}

```dart
return Scaffold(
  body: Column(
    children: [
      Expanded(
        child:
        Row(
          children: [
            Container(
              width: 200,
              child: Text("data"),
            ),
            Expanded(
              child: Text("data"),
            )
          ],
        ),
      ),
     Text("data")
    ],
  ),
);
```
다음과 같이 row 를 expanded 로 묶으니, 아래 Text 높이를 제외한 나머지 높이를 row 가 가지게 되었다.

이렇게 column의 위쪽 길이를 더 늘릴 수 있게 되었다.
