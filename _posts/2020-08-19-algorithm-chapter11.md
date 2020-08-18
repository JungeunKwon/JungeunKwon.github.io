---
layout: post
title: "[백준] 다리만들기 2 - 17472"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, 크루스칼, 그리디, 유니온파인드]
description: "백준 17472 다리만들기 2 풀이"
featured: false
---

![17825](/assets/images/boj/17472_boj.png)

이번 문제는 bfs 와 크루스칼 알고리즘을 이용하여 풀이를 하였다. 크루스칼 알고리즘은 [[프로그래머스]섬 연결하기](/algorithm-chapter10/)에 설명을 참고하길 바란다. 

```java
boolean visited[][] = new boolean[N][M];
int islandNum = 1;
Queue<Info> q = new LinkedList<>();
for (int i = 0; i < N; i++) {
	for (int j = 0; j < M; j++) {
		if (arr[i][j] == 1 && !visited[i][j]) { //arr 는 입력 받은 값을 저장해둔 배열이다.
			q.clear();
			q.offer(new Info(i, j));
			visited[i][j] = true;
			arr[i][j] = islandNum;
			getNum(islandNum, q, visited);
			islandNum++;
		}
	}
}
private static void getNum(int islandNum, Queue<Info> q, boolean[][] visited) {
	int tx = 0, ty = 0;
	while (!q.isEmpty()) {
		Info temp = q.poll();
		for (int i = 0; i < 4; i++) {
			tx = temp.j + dx[i];
			ty = temp.i + dy[i];
			if (tx < 0 || ty < 0 || tx >= M || ty >= N)
				continue;
			if (arr[ty][tx] == 0)
				continue;
			if (visited[ty][tx])
				continue;
			arr[ty][tx] = islandNum;
			visited[ty][tx] = true;
			q.offer(new Info(ty, tx));
		}
	}
}
```

해당 과정을 통해 그림처럼 각 색칠된 부분 즉 섬의 영역에 번호를 매겨 각 섬마다 구분이 되게 해두었다.  파란색은 1번 초록색은 2번 노란색은 3번 빨간색은 4번 이런식이다. for 문을 돌면서 입력 받은 배열의 값이 1이면 섬 영역이고 방문하지 않은 곳이면 새로운 섬이기에 해당 좌표를 큐에 넣고 bfs를 시작한다. 0이면 탐색하지 않는 식으로 하여 붙어있는 1을 모두 조사해 섬 번호를 붙여 주며 방문표시를 해준다. 

```java
visited = new boolean[N][M];
List<Bridge> list = new ArrayList<>();
for (int i = 0; i < N; i++) {
	for (int j = 0; j < M; j++) {
		if (arr[i][j] != 0 && !visited[i][j]) {
			q.clear();
			q.offer(new Info(i, j));
			visited[i][j] = true;
			getBridge(arr[i][j], q, visited, list);
		}
	}
}
private static void getBridge(int islandNum, Queue<Info> q, boolean[][] visited, List<Bridge> list) {
	int tx = 0, ty = 0;
	while (!q.isEmpty()) {
		Info temp = q.poll();
		for (int i = 0; i < 4; i++) {
			tx = temp.j + dx[i];
			ty = temp.i + dy[i];
			if (tx < 0 || ty < 0 || tx >= M || ty >= N)
				continue;
			if (visited[ty][tx])
				continue;
			if (arr[ty][tx] == islandNum) {
				visited[ty][tx] = true;
				q.offer(new Info(ty, tx));
				continue;
			}

			if (arr[ty][tx] == 0) {
				int cost = 1;
				while (true) {
					tx = tx + dx[i];
					ty = ty + dy[i];
					if (tx < 0 || ty < 0 || tx >= M || ty >= N)
						break;
					if (arr[ty][tx] == islandNum)
						break;
					if (arr[ty][tx] != 0 && arr[ty][tx] != islandNum) {
						if (cost < 2)
							break;
						list.add(new Bridge(islandNum, arr[ty][tx], cost));
						break;
					}
					cost++;
				}
			}
		}
	}
}
```

섬 번호를 다 매겼으면 이제 다리를 놓아준다. for 문을 돌면서 0이 아니면 섬이란 뜻이고 방문하지 않았으면 다리를 안 놓았다는 뜻이므로 해당 좌표를 q에 넣고 다리 놓는 bfs를 시작한다. bfs 를 하면서 자기 자신의 섬 번호면 내 옆칸이라는 뜻이고 내 옆칸에서도 다리를 놓아봐야하기 때문에 q에 넣어준다. 만약 0 이면 다리를 놓을 수 있는 지 확인을 해준다. 문제에서 다리는 직진으로만 놓을 수 있기 때문에 조금 더 쉽게 풀 수 있었다. 0인 경우 다른 섬 번호를 만날 때 까지 while 문을 돌며 한칸한칸 이동해준다. 이때 U이렇게 생긴 섬이 존재하는 예제가 있기 때문에 내 섬번호를 만났으면 더이상 가면 안되기에 break를 해준다. 또한 문제에서 비용이 2 이하인 다리는 지을 수 없기에 break를 해준다. 만약 모든 조건이 충족하면 Bridge 리스트에 넣어준다. 

다리를 다 만든 이후에 크루스칼 알고리즘을 시작하면 된다. 

```java
int unioncount = 0;
for (int i = 1; i < islandNum; i++) {
	if (parent[i] == i)
		unioncount++;
}
if (unioncount != 1)
	cost = 0;
if (bridgecnt != islandNum - 2)
	cost = 0;
```

크루스칼 알고리즘은 모든 노드들이 연결되어야 하므로 하나라도 연결되지 않은 섬이 있다면 -1을 출력해준다. 또한 간선의 개수는 노드 - 1 개 여야 하므로 해당 조건을 걸어준다. 여기서 islandNum은  노드 + 1로 되어 있기 때문에 - 2를 해주었다. 

<script src="https://gist.github.com/JungeunKwon/135284cdb8f30b0860bce58a368cd113.js"></script>