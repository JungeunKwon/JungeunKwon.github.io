---
layout: post
title: "숫자고르기 - 2668"
author: Jungeun
categories: [algorithm, 백준]
tags: [알고리즘, 백준, dfs]
description: "백준 2668 숫자고르기 풀이"
featured: false

---

![1507](/assets/images/boj/2668_boj.png)

처음에는 dfs 부분집합 구하는 것 처럼 선택 하고 안하고를 반복한 뒤 마지막에 검사하는식으로 구현하였더니  메모리 초과가 일어 났다. 

반례를 보다가 이해하기 쉬운 반례를 찾아 그려보았다.  배열 N 번까지 for문을 돌면서 각각 싸이클을 찾는다. 우선 첫번째 배열이 origin 넘버로 1번을 가지고 dfs 를 들어간다 -> 1번의 값은 2이다. -> 2번이 방문했던 곳이면 탐색이 끝난다. -> 방문한 곳이 아니면 2번 배열로 가서 값을 본다. -> 2번 값이 3이고 방문 체크 후 또 dfs 로 다음을 찾는다. -> 4의 값이 origin 넘버이므로 싸이클이 형성 되었다는 것을 알 수 있다. 이때 flag 를  true 로 변경한 뒤 거쳐온 경로들을 최종 리스트에 넣어주고 방문 체크를 한다.  

<script src="https://gist.github.com/JungeunKwon/d69584af3227954709251f954be89999.js"></script>