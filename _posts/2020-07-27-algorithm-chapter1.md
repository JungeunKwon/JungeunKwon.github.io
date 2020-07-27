---
layout: post
title: "궁금한 민호 - 1507"
author: Jungeun
categories: [algorithm, 백준]
tags: [알고리즘, 백준, 플로이드와샬]
description: "백준 1507 궁금한 민호 풀이"
featured: false

---

![1507](/assets/images/boj/1507_boj.png)

그림 그리면서 따라 해보니 이해가 잘 되었다.

문제를 보면 다행히도 최단거리는 이미 다 구해져있다. 

주어진 예제에서 1번에서 2번가는 거리가 6, 2번에서 3번으로 가는 거리가 9 이고, 이 둘의 합이 15인 것을 알 수 있다. 예제 입력에서 1번에서 3번으로 가는 거리가 15인 것으로 보아 1은 2를 거쳐가는 것이 최단 거리라고 유추할 수 있다. 그래서 1번에서 3번 가는 것은 거쳐가는 것이기에 제외한다. 이런식으로 거쳐가는 거리를 제외하고 남은 다리를 더하면 된다.

불가능한 경우란 이미 최단거리가 구해져 있는 데, 거쳐 가는 것이 더 짧은 경우 불가능한 경우이다. (28번 줄)

<script src="https://gist.github.com/JungeunKwon/fe8e18ee06a1bcb3c93515cb716b3587.js"></script>