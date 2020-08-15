---
layout: post
title: "[백준] 어른 상어 - 19237"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, 구현]
description: "백준 19237 어른 상어 풀이"
featured: false

---

![19237](/assets/images/boj/19237_boj.png)

이번 문제도 구현 문제로 조건을 꼼꼼하게 잘 읽어보고 풀이를 해야 한다. 우선 번호가 작은 상어가 높은 상어를 쫓아낼 수 있기에 번호가 큰 상어부터 움직임을 시작한다. 고려해줘야 하는 점은 상어가 동시에 도착했을 때이다. 우선 상어들을 움직여주고, 다시 움직임을 시작할때 아까 움직였던 배열위치에 내 번호가 아니면 작은 상어에 의해 쫓겨난 것이므로 죽는 처리를 해준다. 

<script src="https://gist.github.com/JungeunKwon/c636342bd52986b24954ffd9b656ccff.js"></script>