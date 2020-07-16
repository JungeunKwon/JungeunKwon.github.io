---
layout: post
title: "스레드"
author: Jungeun
categories: [operating-system]
tags: [OS, operating-system, 공룡책, 스레드]
description: "스레드에 대한 설명"
featured: false
---

### Threads

------

스레드는 프로세스의 작업 흐름을 말한다. 하나의 프로세스가 동시에 여러 작업을 수행하는 것을 멀티스레드라고 한다. 두 프로세스가 하나의 데이터를 공유하려면 메시지 패싱이나 공유 메모리 또는 파이프를 사용해야하는데, 효율이 떨어지고 프로세스 사이 컨텍스트 스위치가 계속 일어나면 성능 저하가 발생한다. 스레드 전환에도 컨텍스트 스위치가 일어나지만 속도가 더 빠르다. 

### Thread Pools

------

스레드를 요청할  때마다 매번 새로운 스레드를 생성하고, 수행하고, 지우고를 반복하면 성능이 저하된다. 그래서 미리 스레드풀에 여러 개의 스레드를 만들어두고 요청이 오면 스레드 풀에서 스레드를 할당해주는 방법을 사용한다.

### Multicore Programming 

------

멀티코어 또는 멀티프로세서 시스템을 구현할 때는 동시성(Concurrency)와 병렬성(Parallelism)을 알아야 한다. 

동시성: 싱글 프로세서 시스템에서 사용되는 방식으로, 프로세서가 여러 개의 스레드를 번갈아가며 수행함으로써 동시에 실행되는 것처럼 보이게 하는 방식

병렬성: 멀티코어 시스템에서 사용되는 방식으로, 여러 개의 코 어가 각 스레드를 동시에 수행하는 방식

### 멀티 쓰레드

------

하나의 프로세스 내에서 둘 이상의 스레드가 동시에 작업을 수행하는 것

하나의 프로세스를 다수의 실행 단위로 구분하여 자원을 공유하고 자원의 생성과 관리의 중복성을 최소화하여 수행 능력을 향상시키는 것을 멀티 쓰레딩이라고 한다.  메모리를 공유하므로 시스템 자원의 낭비가 적다. 
전역 변수의 공간 또는 동적으로 할당된 공간인 힙영역을 이용하여 데이터를 주고받는다.(데이터 영역과 힙 영역)

> 멀티 프로세스와 멀티 스레드 모두 여러 흐름을 동시에 수행한다는 공통점을 가지고 있지만, 멀티 프로세스는 각 프로세스가 독립적인 메모리를 가지고 별도로 실행되지만, 멀티 스레드는 각 스레드가 자신이 속한 프로세스의 메모리를 공유한다는 차이점이 있다.
>
> 멀티 프로세스들은 서로 독립적이여서 하나의 프로세스에서 오류가 발생해도 다른 프로세스에게 영향을 미치지 않지만 멀티 스레드는 하나의 스레드가 예외를 발생시키면 프로세스 자체가 종료될 수 있다.



출처: 

https://parksb.github.io/ [공룡책으로 정리하는 운영체제]