---
layout: post
title: "[운영체제] Process 2"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "동기식 입출력과 비동기식 입출력, 프로세스 스케줄링 큐의 모습, Thread"
featured: false
---

1. Thread - 프로세스 하나에서 공유할 수 있는 건 최대한 공유하고 CPU 수행과 관련된 정보는 별도로 가지고 있음

   ![process state](/assets/images/operating-system/thread.png)

   - Thread : CPU 수행 단위 , 프로그램 실행 단위

   - Thread 의 구성

     - program counter - 각 스레드마다 코드의 어느 부분을 수행하고 있었는 지
     - register set - 각 스레드마다 어떤 레지스터 값을 사용하는 지
     - stack space - 각 스레드마다 호출한 함수의 정보

   - Thread 가 동료 Thread 와 공유하는 부분(=task)

     - code section

     - data section

     - OS resources

       메모리 주소공간과, 프로세스 상태와 프로세스의 각종 자원들은 공유

2. Thread 장점
   - 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running) 되어 빠른 처리를 할 수 있다.
   - 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput) 과 성능 향상을 얻을 수 있다.
   - 스레드를 사용하면 병렬성을 높일 수 있다.(CPU가 여러개 달린 컴퓨터에서만)
   - 응답성 - 사용자 입장에서 빠르다. ex) 하나의 스레드가 그림파일등을 들고오는 동안에 다른 스레드가 이미 읽어온 HTML 문서들을 화면에 뿌려준다.
   - 자원 공유 - 자원 공유를 통해 자원을 효율적으로 사용
   - 경제성 - 컨텍스트 스위칭은 overhead가 크지만 스레드간의 스위칭은 overhead가 적다. 즉, 같은일을 하는 것이라면 프로그램을 여러개 두는 것보다 스레드를 여러개 두는 것이 더 효율적이다.



*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

