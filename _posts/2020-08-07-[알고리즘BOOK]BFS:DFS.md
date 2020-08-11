---
layout: post
title:  "DFS/BFS"
date:   2020-08-07 10:11:19 +0900
categories: [algorithm]
use_math: true
---

## BFS

BFS와 DFS은 완전탐색처럼 graph 전체를 탐색하는 방식이지만 조금더 효율적으로 탐색하는 방식이다. Breadth First Search (BFS)은 너비우선탐색을 의미하며 graph node에서 인접한 모든 노드를 차례대로 방문하는 방식이다.

<img src="https://raw.githubusercontent.com/jsstar522/jsstar522.github.io/master/static/img/_posts/20200807/1.png" alt="distribution" style="display:block; width:700px; margin: 0 auto;"/>

BFS 구현의 핵심은 queue을 이용하는 것이다. queue은 먼저 들어온 요소가 먼저 나가는 방식이다. 위의 그림을 보면 1을 먼저 집어넣고, 1와 연결된 node (주황색)을 모두 집어 넣었다. 주황색 중 가장 먼저 들어간 node은 2이고 2를 `pop` 한 뒤 2와 연결된 node인 6, 7, 8, 9가 queue에 들어간다. 이렇게 탐색하는 방식을 BFS라고 한다.

코드로 구현해보기 위해 위 그림의 graph을 만들어보자.

```python
graph = {
  '1' : ['2', '3', '4', '5'],
  '2' : ['1', '6', '7', '8', '9'],
  '3' : ['1', '10', '11'],
  '4' : ['1'],
  '5' : ['1'],
  '6' : ['2'],
  '7' : ['2'],
  '8' : ['2'],
  '9' : ['2'],
  '10' : ['3'],
  '11' : ['3'],
}
```

각 node별로 연결되어 있는 node가 모두 value로 들어가 있다. 이제 BFS을 이용하여 모든 node을 탐색해보자.

```python
q = []
visited = []
q.append('1')
visited.append('1')

def bfs():
  while q:
    now = q.pop(0)
    print(now)
    for node in graph[now]:
      if not node in visited:
        visited.append(node)
        q.append(node)

bfs()

# 출력
# 1
# 2
# 3
# 4
# 5
# 6
# 7
# 8
# 9
# 10
# 11
```



## DFS

Depth Frist Search (DFS)은 깊이우선탐색을 의미하며 막다른 길이 나올 때까지 인접한 node을 타고 내려가는 방식이다. DFS은 stack을 이용한다. 먼저 들어간 node가 먼저 빠져나온다. 



DFS 재귀 백트래킹