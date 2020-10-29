---
layout: post
title: "[운영체제] CPU Scheduling"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "CPU and I/O Bursts in Program Execution, CPU-burst Time의 분포, 프로세스의 특성 분류, CPU Scheduler & Dispatcher"
featured: false
---

1. 프로세스의 특성 분류

   프로세스는 그 특성에 따라 다음 두 가지로 나뉨

   - I/O-bound process - 사람과 인터렉션 하는 작업
     - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
     - (many short CPU bursts) - 중간에 I/O까 끼어들어 CPU 버스트는 줄어들지만 빈번하다
   - CPU-bound process
     - 계산 위주의 job
     - (few very long CPU bursts) - 중간에 I/O까 끼어들지 않아 CPU버스트 시간이 길지만 빈번하진 않다

2. CPU Scheduler & Dispatcher

   - CPU Scheduler - CPU를 누구한테 줄 지 고르는 것

     - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다

   - Dispatcher - CPU를 넘겨 주는 것

     - CPU의 제어권을 CPU scheduler 에 의해 선택된 프로세스에게 넘긴다.
     - 이 과정을 context switch(문맥 교환)이라고 한다.

   - CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.

     - Running -> Blocked (ex : I/O 요청하는 시스템 콜)
     - Running -> Ready (ex : 할당 시간 만료로 timer interrupt)
     - Blokced -> Ready (ex : I/O 완료후 인터럽트) - 보통은 I/O 인터럽트가 오면 진행중인 프로그램을 멈추고 I/O온 프로그램을 Ready 상태로 만들고 진행중이던 프로그램에게 CPU를 할당해주는 방식이지만 Priority 에 기반한 스케줄링은 I/O인터럽트 발생 시 I/O 인터럽트 요청한 프로그램이 우선순위가 더 높다면 전에 프로그램에서 CPU를 가져와 I/O인터럽트 요청한 프로그램에게 준다.
     - Terminate

     1, 4 에서의 스케줄링은 nonpreemptive(= 강제로 빼앗지 않고 자진 반납)

     나머지는 preemptive(= 강제로 빼앗음)

3. Scheduling Algorithms

   - **FCFS (First-Come First-Served)**
   - **SJF (Shortest-Job-First)**
   - **SRTF (Shortest-Remaining-Time-First)**
   - **Priority Scheduling**
   - **RR (Round Robin)**
   - **Multilevel Queue**
   - **Multilevel Feedback Queue**

4. Scheduling Criteria - Perfomance Index(= Perfomance measure, 성능 척도)

   - CPU utilization (이용률) - 시스템 입장에서 성능 척도
     - 전체 시간 중 CPU가 놀지 않고 일한 시간
   - Throughput (처리량) - 시스템 입장에서 성능 척도
     - 주어진 시간 동안 몇개의 작업을 완료 했느냐 
   - Turnaround time (소요시간, 반환시간) - 프로그램 입장에서 성능 척도
     - CPU를 쓰러 들어와서 다 쓰고 나갈때 까지 걸린 시간 ( 줄 서고 종료될때 까지)
   - Waiting time (대기 시간) - 프로그램 입장에서 성능 척도
     - CPU를 쓰기 위해 ready queue 에서 기다리는 시간
   - Response time (응답 시간) - 프로그램 입장에서 성능 척도
     - ready queue에 들어와서 처음으로(최초로) CPU를 얻기 위해 걸린 시간

5. FCFS (First-Come First-Served)

   ![fcfs](/assets/images/operating-system/fcfs.png)

   먼저 온 순서대로 먼저 처리한다. 비선점 스케줄링

   CPU를 오래 쓰는 프로그램이 먼저 도착해서 CPU를 사용해 버리면 CPU를 짧게 쓰는 작업들이 계속 기다려야 한다. 반면에 CPU를 짧게 쓰는 프로그램들이 도착하면 평균 기다리는 시간이 짧아진다.

   Convy effect : 긴 프로세스로 인해 짧은 프로세스가 지나치게 오래 기다리는 현상

6. SJF (Shortest-Job-First)

   - 각 프로세스와 다음번 CPU burst time 을 가지고 스케줄링에 활용

   - CPU burst time 이 가장 짧은 프로세스를 제일 먼저 스케줄

     ![npsjf](/assets/images/operating-system/npsjf.png)

   - **Nonpreemptive** : 일단 CPU를 잡으면  이번 CPU burst 가 완료될 때 까지 CPU를 선점(preemptive)당하지 않음

     ![psjf](/assets/images/operating-system/psjf.png)

   - **Preemptive** : 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU burst time 을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김, 이 방법을 **Shortest-Remaining-Time-First(SRTF)**라고도 부른다.

   - 주어진 프로세스들에 대해 **minimum average waiting time** 을 보장 - preemptive 버전

   - CPU사용이 긴 프로그램은 영원히 CPU를 못 받을 수 있다. (CPU 시간이 짧은 프로그램이 계속해서 Queue에 도착) - Starvation (기아) 현상 

   - CPU 사용 시간을 미리 알 수 없다. 과거의 CPU burst time 을 이용해서 추정만이 가능하다.

7. Priority Scheduling

   - 우선순위가 제일 높은 프로그램에게 CPU를 넘겨준다.
   - **Nonpreemptive** : 실행 중인 프로그램 보다 더 높은 우선순위를 가진 프로그램이 도착해도 CPU를 빼앗기지 않는다.
   - **Preemptive** : 실행 중인 프로그램 보다 더 높은 우선순위를 가진 프로그램이 도착하면 CPU를 빼앗긴다.
   - SJF는 일종의 prority scheduling 이다
   - 우선 순위가 낮은 프로그램이 지나치게 오래 기다릴 수 있다. - Starvation (기아) 현상 
   - 해결을 위해 Aging 기법 사용 - 우선순위가 낮은 프로그램이라도 오래 기다리면 우선순위를 높여준다.

8. RR (Round Robin)

   ![rr](/assets/images/operating-system/rr.png)

   - 현대 운영체제에서 사용되는 스케줄링, 응답시간이 빠르다.
   - 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가진다.(일반적으로 10-100 miliseconds)
   - 할당 시간이 지나면 프로세스는 선점(preempted) 당하고 ready queue 의 제일 뒤에 가서 다시 줄을 선다.
   - n 개의 프로세스가 ready queue 에 있고 할당 시간이 q time unit 인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n 을 얻는다. -> 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다. 
   - q 가 아주 클 경우 :  FCFS와 같은 스케줄링이 된다.
   - q 가 아주 작을 경우 :  context switch 오버헤드가 커진다.
   
9. Multilevel Queue - 이 전에는 한줄로 cpu 줄 서기 였는 데 이 후부터는 여러줄로 줄을 선다

   ![mqueue](/assets/images/operating-system/mqueue.png)

   - Ready queue 를 여러 개로 분할
     - foreground (interactive)
     - background (batch - no human interaction)
   - 각 큐는 독립적인 스케줄링 알고리즘을 가짐 - 줄의 특성에 맞는 큐별 스케쥴링 선택
     - foreground - RR
     - background - FCFS
   - 큐에 대한 스케줄링이 필요
     - Fixed priority scheduling
       - 우선 순위 높은 줄이 비지 않으면 우선순위 낮은 줄은 cpu를 받지 못하는 기아 현상 발생
     - Time slice
       - 각 큐에 CPU time 을 적절한 비율로 할당
       - ex ) 전체 CPU 시간의 80퍼는 우선순위 높은 줄 20 퍼는 우선순위 낮은 줄에 배분

10. Multilevel Feedback Queue - 멀티레벨 큐는 줄 간의 이동(신분 상승)을 하지 못하는 문제가 있다.

    ![mfqueue](/assets/images/operating-system/mfqueue.png)

    - 프로세스가 다른 큐로 이동 가능
    - 에이징(aging)을 이와 같은 방식으로 구현할 수 있다.
    - Multilevel-feedback-queue scheduler 를 정의하는 파라미터들
      - Queue 수
      - 각 큐의 scheduling algorithm
      - Process 를 상위 큐로 보내는 기준
      - Process 를 하위 큐로 내쫓는 기준
      - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

    ![exmfqueue](/assets/images/operating-system/exmfqueue.png)

    - CPU 시간이 짧은 프로그램에게 우선순위를 더 많이 준다.
    - CPU 사용 시간이 긴 지 짧은 지 예측이 필요가 없어진다.

11. Multiple-Processor Scheduling

    - CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
    - Homogeneous processor 인 경우
      - Queue 에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
      - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐.
    - Load sharing
      - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
      - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
      - 각각 CPU 마다 별도의 줄을 서게 하는 방식 모든 CPU가 적절히 일을 한다
    - Symmetric Multiprocessing (SMP)
      - 각 프로세서가 각자 알아서 스케줄링 결정 - 모든 CPU 가 대등
    - Asymmetric multiprocessing
      - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름 - CPU가 여러개 있는 데 그 중 하나의 CPU가 전체적인 컨트롤을 담당

12. Real-Time Scheduling 

    - Hard real-time systems
      - Hard real-time task 는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
    - Soft real-time computing
      - Soft real-time task 는 일반 프로세스에 비해 높은 priority를 갖도록 해야 함

13. Thread Scheduling

    - Local Scheduling - os 가 하는 게 아니라 사용자 프로그램이 직접 스케줄링
      - User level thread 의 경우 사용자 수준의 thread library 에 의해 어떤 thread 를 스케줄할지 결정
    - Global Scheduling
      - Kernel level thread의 경우 일반 프로세스와 마찬 가지로 커널의 단기 스케줄러가 어떤 thread 를 스케줄할지 결정

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

