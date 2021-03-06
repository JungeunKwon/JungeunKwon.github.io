---
layout: post
title: "[백준] 미네랄 - 2933 (Java)"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, bfs, 구현]
description: "백준 2933 미네랄 풀이 (Java)"
featured: false

---

![2933](/assets/images/boj/2933_boj.png)

이번 문제는 구현 문제로 조건을 잘 따라하면 된다. 처음에는 스틱으로 그 줄을 지우는 줄 알았으나 한 칸을 지우는 것이였다. 

처음에는 클러스터의 맨 아래 부분이 바닥 또는 다른 클러스트에 닿으면 끝인 줄 알았으나 그림과 같이 예상치 못한 테스트 케이스가 있어 문제를 다시 풀게 되었다.  해결 방법은 우선 분리된 클러스터가 있으면 1번이라는 번호를 매긴 배열을 둔다. 그리고 배열을 검사하면서 1번이면서 내 아랫칸이 0이고 입력의 캐릭터 배열에서도 빈칸인 경우에는 빈칸이 아닐때 까지 칸 수를 헤아린다. 이때 1번 매긴 내 자신의 클러스터를 만나면 다음 칸부터 다시 보도록 한다. 이 과정을 반복하면서 칸 수의 최솟값을 찾는다. 그러고 캐릭터 배열에서 최소 칸 수 만큼 클러스터를 밑으로 내려준다. 

<script src="https://gist.github.com/JungeunKwon/ebf29171e07261bc6c16a4e36147774a.js"></script>

*출처: 백준* https://www.acmicpc.net/problem/2933