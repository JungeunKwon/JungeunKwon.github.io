---
layout: post
title: "[운영체제] Process Synchronization 2"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Semaphores, Implementation, Classical Problems of Syncronization, Bounded-Buffer Problem, Readers-Writers Problem, Dining-Philosophers Problem, Monitor"
featured: false
---

1. Classical Problems of Synchronization
   - Bounded-Buffer Problem (Producer-Consumer Prolem)
   - Readers and Writers Problem
   - Dining-Philosophers Problem

2. Bounded-Buffer Problem

   ![boundedbuffer](/assets/images/operating-system/boundedbuffer.png)

   1. 버퍼의 크기가 유한한 환경에서 생산자-소비자 문제이다.
   2. 프로세스가 생산자 프로세스 소비자 프로세스가 있다.
   3. 생산자 프로세스는 공유 데이터에 데이터를 만들어 집어 넣는 역할을 한다.
   4. 소비자 프로세스는 공유 데이터에 데이터를 꺼내가는 역할을 한다.
   5. 생산자가 두명이 동시에 빈 공간에 도착해서 공간이 비었다고 데이터를 동시에 집어 넣으면 문제가 발생한다. 그래서 락을 걸어서 데이터를 집어넣는 과정이 필요함
   6. 소비자가 두명이 동시에 데이터가 있는 공간에 도착해 동시에 데이터를 꺼내면 문제가 발생한다. 이것 또한 락을 걸어서 데이터를 꺼내는 과정이 필요하다.
   7. 버퍼의 크기가 유한하기 때문에 생산자가 와서 데이터를 집어 넣고 또 다른 생산자가 와서 데이터를 집어 넣을려고 하는 데 버퍼가 다 차 있으면 소비자가 나타나서 데이터를 꺼내갈 때 까지 기다려야 한다. 
   8. 반대로 소비자 입장에서는 소비자가 와서 데이터를 다 꺼내가고 다른 소비자가 와서 데이터를 꺼낼려고 하는 데 버퍼가 비어 있기 때문에 생산자가 데이터를 넣을때 까지 기다려야 한다.

3. Readers-Writers Problem

   - 한 process 가 DB에  wirte 중일 때 다른 process 가 접근하면 안됨

   - read는 동시에 여럿이 해도 됨 ( 쓰는 작업은 동시에 하면 안되지만 읽는 작업은 여러명이 해도 문제가 발생하지 않는데 lock을 걸어 버리면 비효율적인 문제가 발생한다. read가 도착했을 땐 동시에 읽게 해줘야 한다.)

   - solution

     - Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB 에 접근하게 해준다
     - Writer 는 대기 중인 Reader 가 하나도 없을 때 DB 접근이 허용된다.
     - 일단 Writer가  DB에 접근 중이면 Reader 들은 접근이 금지된다
     - Writer가 DB에서 빠져나가야만 Reader의 접근이 허용된다.

   - Shared Data

     - DB 자체
     - readcount : 현재 DB에 접근 중인 Reader의 수

   - Synchronization variables

     - mutex : 공유 변수 readcount 를 접근하는 코드 (critical section)의 mutual exclusion보장을 위해 사용
     - db : Reader 와 writer 가 공유 DB 자체를 올바르게 접근하게 하는 역할

   - 해결 방법

     ![readerswriters](/assets/images/operating-system/readerswriters.png)

     - readcount 라는 변수를 두어 내가 최초의 읽는 사람이라면 읽는동안 write 를 하지 못하게 lock를 건다 하지만 최초의 읽는 사람이 아니라면(readcount > 1) 아무것도 하지 않고 데이터를 읽는다.
     - readcount 를 변경시킬 때도 공유변수기 때문에 동기화 문제가 발생할 수 있으므로 mutex lock를 걸어준다.
     - DB를 읽는 작업이 끝났으면 빠져나가는 데 내가 마지막으로 읽고 빠져나가는 프로세스라면 (readcount == 0 ) 읽지 못하게 걸었던 lock 을 풀어준다.

   - 여기서는 writer 가 도착해서 쓸려고 하는 데 reader 가 락을 걸어두면 모든 reader 가 빠져나갈때 까지 기다려야 하는데 reader 가 계속 들어오면 writer가 기아 현상 발생 가능 -> 어느 정도 리더가 지나가면 reader 가 못 오게 막고 writer 가 작업하게 해주면 됨

4. Dining-Philosophers Problem

   ![diningphilo](/assets/images/operating-system/diningphilo.png)

   - 철학자는 생각하고, 밥먹고 두 가지 업무가 있다.
   - 각각 철학자는 생각하는 시간과 밥먹는 시간이 다르다.
   - 밥을 먹을려면 왼쪽과 오른쪽에 있는 젓가락 하나씩을 잡아야 먹을 수 있다. 두명이 동시에 한 젓가락을 가질 수 없다. 
   - 밥 먹고 나면 젓가락을 원위치에 둔다.
   - P(chopstick[i]) 왼쪽 젓가락 잡는 행위
   - P(chopstick[(i+1) % 5]) 오른쪽 젓가락 잡는 행위
   - 앞의 Solution 에서는 deadlock 의 문제가 있다.
     - 모든 철학자가 동시에 밥을 먹을려고 왼쪽 젓가락을 잡으면 오른쪽 젓가락을 잡지 못한다. 본인이 밥을 먹고 배가 부르기전까지는 한번 잡은 젓가락을 놓지 않기 때문 왼쪽을 잡고 오른쪽 젓가락을 가질 수 있게 무한히 기다린다.
   - 해결 방안
     - 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
     - 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다.
     - 비대칭 : 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 잡도록

5. Monitor 

   세마포어와 모니터는 프로세스 싱크로나이즈 문제를 프로그래머 입장에서 어떻게 쉽게 구현할까를 제공해준다. 세마포어를 사용하더라도 불편한 점이 있기 때문에 모니터를 이용해서 프로그래머를 좀 더 편안하게 해준다. 모니터를 이용해서 생산자 소비자 문제, 철학자 식사 문제를 해결 할 수 있다. 

   - Semaphore 문제점

     - 코딩하기 힘들다
     - 정확성의 입증이 어렵다
     - 자발적 협력이 필요하다
     - 한번의 실수가 모든 시스템에 치명적 영향 -> 문제가 생겼을 때 버그 잡기가 어려움

     ![monitor](/assets/images/operating-system/monitor.png)

     시작 전에 P연산을 하지 않고 V 연산을 해버리는 경우 동시에 두개 들어감

     P연산을 하고 P연산을 lock 이 풀리지 않아 무한 기다림

   - 동시 수행중인 프로세스 사이에서 abstract data type 의 안정한 공유를 보장하기 위한 high-level synchronization construct

     ![monitor2](/assets/images/operating-system/monitor2.png)

     공유데이터를 밖에서 아무나 접근할 수 있게 하는 게 아니라 모니터 안에 공유 데이터와 공유 데이터를 접근하는 프로시져를 정의해 두고 해당 프로시져를 통해서만 접근할 수 있게 해두는 것 .그리고 모니터가 원천적으로 동시에 여러 프로시져가 실행이 되지 않게 통제를 한다. 프로그래머 입장에서는 lock 를 걸 필요가 없어진다. 모니터가 알아서 하나의 프로세스만 접근할 수 있게 해주기 때문.

     **세마포어와의 차이점은 lock 을 걸 필요가 없어진다는 것이다.**

   - 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
   - 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없어짐
   - 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 condition variable 사용
   - Condition variable 은 wait 과 signal 연산에 의해서만 접근 가능하다. x.wait(); 을 invoke 한 프로세스는 다른 프로세스가 x.signal() 을 invoke 하기 전까지 suspend된다. x.signal(); 은 정확하게 하나의 suspend 된 프로세스를 resume 한다. Suspend 된 프로세스가 없으면 아무 일도 일어나지 않는다.

   ![boundedbuffer2](/assets/images/operating-system/boundedbuffer2.png)

   생산자든 소비자든 하나의 프로세스만 모니터 안에서 활성화 되기 때문에 lock 을 거는 작업이 없어진다. 생산자 입장에서 비어 있는 버퍼가 없다면 비어있는 버퍼를 기다리는 줄에 block 시켜서 보낸다. 비어 있는 버퍼가 있다면 작업을 하고 잠들어있는 소비자를 깨워주는 일을한다. 소비자 입장에서는 버퍼가 비어 있다면 버퍼가 찰때까지 기다리는 줄에 block 시켜 줄을 세우고 버퍼에 데이터가 있다면 데이터를 가져가고 퍼버가 빌때 까지 기다리는 생산자를 깨워준다.

   -> 세마포어 보다 프로그램 입장에서 더 쉽고 lock 을 걸 필요가 없어 진다. 



*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

