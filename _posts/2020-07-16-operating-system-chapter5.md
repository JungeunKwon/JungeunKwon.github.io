---
layout: post
title: "프로세스 동기화"
author: Jungeun
categories: [operating-system]
tags: [OS, operating-system, 공룡책, 크리티컬섹션, 동기화, race-condition, 뮤텍스, 세마포어]
description: "프로세스 사이에 데이터가 동기화 되지 않는 문제에 대한 설명"
featured: false
---

### Race Condition

------

두 프로세스가 동시에 어떤 변수의 값을 바꾼다면 프로그래머의 의도와는 다른 결과가 나올 것이다. 이처럼 프로세스가 어떤 순서로 데이터에 접근하느냐에 따라 결과 값이 달라질 수 있는 상황을 경쟁 상태(race condition)라고 한다.

### Critical-Section Problem

------

코드상에서 경쟁 조건이 발생할 수 있는 특정 부분을 critical section이라고 부른다. crtitcal section problem 를 해결하기 위해서는 몇가지 조건을 충족해야 한다.

- Mutual exclution(상호 배제) : 이미 한 프로세스가 critical section에서 작업중일 때 다른 프로세스는 critical section에 진입해서는 안된다.
- Progress(진행) : critical section에서 작업중인 프로세스가 없다면 다른 프로세스가 critical section에 진입할 수 있어야 한다.
-  Bounded waiting(한정 대기) : critical section에 진입하려는 프로세스가 무한하게 대기해서는 안된다.

Non-preemptive kernels로 구현하면 임계 영역에 문제가 발생하지 않는다. 하지만 비선점 스케줄링은 반응성이 떨이지기 때문에 슈퍼 컴퓨터가 아니고선 잘 사용하지 않는다.

### Mutex Locks

------

mutex locks 은 여러 스레드가 공통 리소스에 접근하는 것을 제어하는 기법으로, lock 이 하나만 존재할 수 있는 locking매커니즘을 따른다. 이미 하나의 스레드가 critical section에서 작업중인 lock 상태이면 다른 스레드들은 critical section에 진입할 수 없다.

### Semaphores

------

세마포어(semaphore)는 여러 개의 프로세스나 스레드가 critical section에 진입할 수 있는 locking 매커니즘이다. 세마포어는 카운터를 이용해 동시에 리소스에 접근할 수 있는 프로세스를 제한한다. 물론 한 프로세스가 값을 변경할 때 다른 프로세스가 동시에 값을 변경하지는 못한다. 

세마포어의 카운터가 한 개인 경우 바이너리 세마포어, 두 개 이상인 경우 카운팅 세마포어라고 한다. 바이너리 세마포어는 사실상 mutex와 같다.

### Deadlocks and Starvation

------

두 프로세스가 서로 종료될 때까지 대기하는 프로그램을 실행한다고 할때, 프로세스 A는 B가 종료될 때까지, 프로세스 B는 A가 종료될  때까지 작업을 하지 않기 때문에 프로그램은 어떤 동작도 하지 못할 것이다. **이처럼 두 프로세스가 리소스를 점유하고 놓아주지 않거나, 어떠한 프로세스도 리소스를 점유하지 못하는 상태가 되어 프로그램이 멈추는 현상을 데드락(Deadlock)**이라고 한다. 



출처: 

https://parksb.github.io/ [공룡책으로 정리하는 운영체제]

