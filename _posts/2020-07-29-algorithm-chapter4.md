---
layout: post
title: "[백준] 청소년 상어 - 19236"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, dfs, 구현]
description: "백준 19236 청소년상어 풀이"
featured: false

---

![19236](/assets/images/boj/19236_boj.png)

이런 구현 문제는 문제를 잘 읽고 조건을 꼼꼼하게 파악한 후, 조건에 맞게 구현하면 쉽게 풀리는 문제이다. 물고기이동과 상어 이동의 반복 작업을 하기 위해 dfs 를 이용하여 풀이하였다. 

상어가 움직일 때 대각선으로 움직인다고 예를 들면 대각선 1칸, 2칸, 3칸 뒤를 갈 수 가 있다. 1칸 이동한 상어와 2칸 이동한 상어 3칸 이동한 상어는 각각 다르게 움직이기 때문에 자신만의 맵 과 물고기 리스트를 들고 다녀야 한다. 그러므로 배열과 리스트의 깊은 복사를 해야하는 까다로움이 있었다. 

<script src="https://gist.github.com/JungeunKwon/f75425802743b6642063fe471042168c.js"></script>

*출처: 백준* https://www.acmicpc.net/problem/19236