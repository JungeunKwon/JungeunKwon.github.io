---
layout: post
title: "[백준] 역사 - 1613 (Java)"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 백준, 플로이드와샬]
description: "백준 1613 역사 풀이 (Java)"
featured: false

---

![1613](/assets/images/boj/1613_boj.png)

처음에 dfs 를 이용하여 탐색을 하였더니 시간초과가 나서 플로이드와샬을 이용하여 풀게 되었다. 예제를 2차원 배열에 넣고 1이 2보다 앞에 있으니 1을 표시해 둔다. 이런식으로 입력값으로 표를 채우고 난 뒤, 플로이드와샬을 한 결과를 보면 위의 사진과 같다. 플로이드 와샬은 i-j 로 가야할때  i-k 를 갈 수 있고 k-j를 갈 수 있으면 i-j 도 갈 수 있다고 생각하고 문제를 풀면 된다. 

<script src="https://gist.github.com/JungeunKwon/8082ad24cfab8639047358823e375d1b.js"></script>

*출처: 백준* https://www.acmicpc.net/problem/1613