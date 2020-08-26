---
layout: post
title: "[백준] 스타트 택시 - 19238 (Java)"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, dfs, bfs, 구현]
description: "백준 19238 스타트 택시 풀이 (Java)"
featured: false

---

![19238](/assets/images/boj/19238_boj.png)

이번 문제도 구현 문제로 조건을 꼼꼼하게 잘 읽어보고 풀이를 해야 한다. 그리고 택시가 목적지에 도착 했을 때 해당 위치에 승객이 있는 경우도 생각을 해줘야한다. 택시의 도착 위치를 가지고 탐색을 하기 위해 dfs 를 이용 하였다. 

finddistance 를 통해 각 사람마다 최솟값을 구해 min 값을 찾는 부분이 있는 데 모든 사람을 검색하기 때문에 시간이 많이 걸리는 듯 하다.  그

<script src="https://gist.github.com/JungeunKwon/4c6d776707ac8ebab55fc08cffa84664.js"></script>

*출처: 백준* https://www.acmicpc.net/problem/19238