---
layout: post
title: "[운영체제] Process Synchronization 1"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "데이터의 접근, Race Condition, OS에서의 race condition(3/3), Example of a Race Condition, The Critical-Section Problem, OS에서 race condition(1/3), If you preempt CPU while in kernel mode…, Initial Attempts to Solve Problem, 프로그램적 해결법의 충족조건, Algorithm 1, Algorithm2, Algorithm3(Peterson's Algorithm), Synchronization Hardware, Semaphores, Critical Section of n Processes, Block / Wakeup Implementation, Implementation, Two Types of Semaphores, Deadlock and Starvation, Dining-Philosophers Problem"
featured: false
---

1. 데이터의 접근 - 컴퓨터 시스템 안에서 데이터 접근하는 패턴

   ![data](/assets/images/operating-system/data.png)

   데이터가 저장된 위치 - storage box

   실제 연산하는 부분 - execution box

   데이터를 읽기만 하면 문제가 될 게 없지만 데이터를 수정하게 되면 누가 먼저 수정했냐에 따라 결과가 달라지는 문제를 싱크로나이즈 문제라고 한다.

2. Race Condition

   ![racecondition](/assets/images/operating-system/racecondition.png)

   한 Storage box 가 여러 excution box 가 공유하게 되면 데이터의 결과가 원치 않는 결과가 나올 수 있다. 이런 문제를 조율해 줄 필요가 있다. 

3. OS 에서 race condition은 언제 발생하는가?

   - Kernel 수행 중 인터럽트 발생 시

     ![kernelrace](/assets/images/operating-system/kernelrace.png)

     1. 커널에서 Count 를 1증가시키는 명령을 수행 중 
     2. Count 값을 읽어오고 1 증가 시켰다. 이제 저장만 하면 되는 데 
     3. 인터럽트가 발생 그 인터럽트에서는 카운트를 1 감소 시킨다
     4. 인터럽트에서 돌아와서 아까 증가시켰던 값을 저장하게 된다.
     5. 결과적으론 1 증가한 것만 반영이 되었다. 

     -> 중요한 값을 건드리는 동안에는 인터럽트가 들어와도 처리를 하지 않고 작업이 끝난 다음에 인터럽트를 처리하는 방법으로 해결

   - Process 가 System call 을 하여 kernel mode 로 수행 중인데 context switch 가 일어나는 경우

     ![rocessrace](/assets/images/operating-system/processrace.png)

     ![rocessrace2](/assets/images/operating-system/processrace2.png)

     1. A가 어떤 프로세스가 System call을 하여 Count를 증가시킬려고 하는데
     2. 할당 시간이 끝나 B에게 넘어간다. 
     3. B가 System call 을 하여 Count 를 증가시킨다.
     4. B의 할당 시간이 끝나 A에게 다시 돌아와 아까 읽었던 값을 증가 시켰다.
     5. 결과적으로 A와 B가 둘다 1을 증가 시켜 2가 증가 되었어야 하지만 1이 증가 되었다. 

     -> 프로세스가 커널모드에 있을 때는 할당 시간이 끝나도 CPU를 뺏기지 않도록 하고 커널모드가 끝나 유저모드로 돌아오면 CPU를 빼앗도록 한다. 

   - Multiprocessor 에서 shared memory 내의 kernel data

     ![rocessrace2](/assets/images/operating-system/multirace.png)

     CPU 가 여러개인 환경 앞의 방법으로는 해결 되지 않음

     데이터에 접근할 때  lock 을 걸어 다른 CPU가 접근 할 수 없게 하고 연산이 끝난 후 unlock으로 다른 CPU가 접근할 수 있게 해준다.

     1. 커널 전체에 lock 을 걸어 하나의 CPU만 커널 모드에 들어갈 수 있게 하는 방법 -> 비효율적
     2. 각각의 데이터에  lock 을 걸어 해당 데이터 접근만 막는 방법 -> 여러 CPU가 커널에 접근 가능

4. Process Synchronization 문제

   - 공유 데이터(shared data)의 동시 접근(concurrent access)은 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.
   - 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperationg process) 간의 실행 순서(orderly execution)를 정해주는 매커니즘 필요
   - **Race condition**
     - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
     - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라진다.
   - Race condition 을 막기 위해서는 concurrent process는 동기화 (synchronize) 되어야 한다.

5. The Critical-Section Problem

   - n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우

   - 각 프로세스의 code segment 에는 공유 데이터를 접근하는 코드인 **critical section**이 존재

   - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다. 

     ![rocessrace2](/assets/images/operating-system/cirticalsection.png)

     Critical section : 공유 데이터에 접근하는 코드를 뜻한다. 

6. 프로그램적 해결법의 충족 조건

   - Mutual Exclusion (상호 배제)
     - 프로세스 Pi 가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.
   - Progress (진행)
     - 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 한다.
   - Bounded Waiting (유한 대기)
     - 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.
   - 가정
     - 모든 프로세스의 수행 속도는 0보다 크다
     - 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다. 

7. Critical Section 해결 알고리즘

   ![algorithm3](/assets/images/operating-system/algorithm3.png)

   상호 배제, 진행 , 유한 대기의 문제를 모두 충족 하지만 Busy Waiting (=spin lock) 문제가 발생한다.

   Busy Waiting : while 문을 체크하면서 critical section 에 누가 있나 없나 해결하는 데에만 CPU시간을 쓰면서 기다리는 것

8. Semaphores - 추상 자료형

   - 앞의 방식들을 추상화 시킴

   ![semaphore](/assets/images/operating-system/semaphore.png)

   공유 자원을 획득하고 반납하는 것을 세마포어가 해줌

   P 연산 : 세마포어 값(공유 데이터)를 획득하는 과정, 자원이 없으면 생길때 까지 기다림

   V 연산 : 값을 반납하는 과정

   두 연산은 atomic 하게 연산이 실행된다는 것을 가정한다.

   여기서도 busy waiting(=spin lock) 이 생김 자원이 없으면 CPU를 자원 기다리는데 사용하기 때문

   ![blockandwakeup](/assets/images/operating-system/blockandwakeup.png)

   그래서 나온게 block and wakeup 방식 (=sleep lock) : 자원을 할당 받지 못하면 계속 기다리는게 아니라 sleep 으로 블럭시킴 (CPU를 사용하지 않음)

   value : 세마포어 변수

   L : 세마포어 때문에 잠들어있는 프로그램을 연결하기 위한 큐

   ![semaphore2](/assets/images/operating-system/semaphore2.png)

   block and wait 방식으로 구현된 세마포어 

   P연산에서 우선 세마포어 변수 값을 하나 빼고 그 변수 값이 0보다 작으면 자원이 없다는 뜻이므로 block 처리를 시킨다.

   V연산에서는 이제 세마포어 변수 값을 하나 증가 시키고 그 변수 값이 0보다 작으면 잠들어 있는 프로세스가 있다는 뜻이므로 잠들어 있는 프로그램을 깨운다. 0이상이면 잠들어 있는 프로세스가 없다는 뜻

9. Busy wait VS Block and wake up

   - Critical section 의 길이가 긴 경우 Block and wake up이 적당
   - Critical section의 길이가 매우 짧은 경우 Block and wake up 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
   - 일반적으로는 Block and wake up 방식이 더 좋음

10. Two Types of Semaphores

    - Counting semaphore - 자원의 개수가 여러개 있어서 여분의 자료를 가져다 쓸 수 있는 경우
      - 도메인이 0 이상인 임의이 정수값
      - 주로 resource counting 에 사용
    - Binary semaphore(=mutex)
      - 0 또는 1 값만 가질 수 있는 semaphore
      - 주로 mutual exclusion (lock/unlock)에 사용

11. Deadlock and Starvation

    - Deadlock

      - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event 를 무한히 기다리는 현상

      ![deadlock](/assets/images/operating-system/deadlock.png)

      1. P0 이 먼저 CPU를 얻어서 S라는 자원을 얻고 CPU를 빼앗긴다
      2. P1 이 CPU를 얻어서 Q라는 자원을 얻고 S를 얻기 위해서 기다린다.
      3. P0은 Q를 얻기 위해 기다린다. 
      4. 둘이 서로 가진걸 얻기 위해 영원히 기다리는 현상이 발생한다.

      해결 방법 : 자원을 획득하는 순서를 똑같이 정해주면 문제 해결 가능 (ex. S를 얻고 나서 를 획득 가능)

    - Starvation

      - Indefinite blocking : 프로세스가 suspend 된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상 -> 특정한 프로세스만 자원을 계속 할당 받아 다른 프로세스는 자원을 못 받는 현상 발생











*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

