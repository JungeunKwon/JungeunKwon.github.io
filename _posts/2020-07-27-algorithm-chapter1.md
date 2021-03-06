---
layout: post
title: "[백준] 궁금한 민호 - 1507 (Java)"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, 플로이드와샬]
description: "백준 1507 궁금한 민호 풀이 (Java)"
featured: false

---

![1507](/assets/images/boj/1507_boj.png)

이미 구해진 최단거리를 통해 최소 도로를 찾아 그 최소값을 찾는 문제이다. 예제 입력을 통해 그래프를 그려보면 그림과 같이 나온다. 1번에서 3번 가는 경로는 15이다. 이것은 1번에서 2번 가는 경로 6 + 2번에서 3번 가는 경로 9 를 합친 값과 같으므로 1번은 2번을 거쳐 3번을 가는 것이 최단 경로인 것을 알 수 있다. 그러므로 그 값은 필요가 없어 제외를 시킨다. 이렇게 거쳐가는 경로인 요소들을 찾아 제외 시키면 최소 도로만 남게 된다. 

불가능한 경우에는 -1을 출력하도록 한다. 불가능한 경우는 예를들어 1번에서 2번을 거쳐 3번을 가는 값이 14라면 예제에서 1번에서 3번 가는 값이 15이므로 예제에 구해진 값이 최단 거리가 아니게 되므로 불가능한 경우이다. 

플로이드 와샬 알고리즘을 참고하도록,,

<script src="https://gist.github.com/JungeunKwon/fe8e18ee06a1bcb3c93515cb716b3587.js"></script>

*출처: 백준* https://www.acmicpc.net/problem/1507