---
layout: post
title: "[백준] 주사위 윷놀이 - 17825"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, 조합]
description: "백준 17825 주사위 윷놀이 풀이"
featured: false

---

![17825](/assets/images/boj/17825_boj.png)

이번 문제는 윷판을 어떻게 표시하냐에 따라 풀이가 다 다르다. 나는 그림과 같이 판에 번호를 매기고 nextpos 라는 배열에 해당 칸에서 다음 칸의 번호를 저장해두었다. 우선 중복 조합으로 말 번호의 조합을 만들고 해당 조합에서 게임을 진행 해보았다. 한칸 한칸 보는 것 때문에 시간이 조금 걸리는 듯 하다. for 문안에 복잡해 보이는 if 문은 꺽이는 부분일 때 그 칸이 지나가는 칸이면 직접 다음칸의 번호를 매겨 주었다.

<script src="https://gist.github.com/JungeunKwon/d14f9e25103ff6d542256ea4705f6d16.js"></script>