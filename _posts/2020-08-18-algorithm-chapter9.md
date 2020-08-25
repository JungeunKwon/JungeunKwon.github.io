---
layout: post
title: "[프로그래머스] 단속카메라 - 42884"
author: Jungeun
categories: [algorithm]
tags: [알고리즘, 프로그래머스, 그리디]
description: "프로그래머스 42884 단속카메라 풀이"
featured: false

---

![단속카메라](/assets/images/programmers/42884_pro_1.png)

이번 문제는 그리디 기초를 다지기 위한 문제이다.

![단속카메라2](/assets/images/programmers/42884_pro_2.png)

그림과 같은 예제가 있다고 생각해보자. 고속도로에서 나가는 번호를 오름차순으로 정렬을 해보면 가장 작은 진출번호가 겹치는 구간까지 한 씨씨티비로 관찰할 수 있다. 그리고 진입 번호가 진출 번호보다 큰 경우 마지막 경우처럼 구간이 겹치지 않고 끊어져 있다는 것을 알 수 있다. 그때는 새로운 씨씨티비를 설치해주고 최솟값을 해당 차의 진출번호로 갱신시켜주면 쉽게 풀 수 있다. 

<script src="https://gist.github.com/JungeunKwon/044c23f72ece03c0ea7116a77e249fd7.js"></script>

*출처: 프로그래머스 코딩 테스트 연습,* https://programmers.co.kr/learn/challenges