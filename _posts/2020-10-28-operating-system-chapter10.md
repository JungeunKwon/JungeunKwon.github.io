---
layout: post
title: "[운영체제] Process 1"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "프로세스의 개념, 프로세스의 상태(Process State), 프로세스의 개념, 프로세스 상태도, Process Control Block(PCB), 문맥교환(Context Switch), 프로세스를 스케줄링하기 위한 큐, Ready Queue와 다양한 Device Queue, 스케줄러(Scheduler)"
featured: false
---

1. 프로세스 :  실행중인 프로그램

2. 프로세스의 문맥(context) : 프로세스의 현재 상태를 알아내는 데 필요한 모든 요소

   - CPU 수행 상태를 나타내는 하드웨어 문맥  : 현재 시점에 프로그램이 인스트럭션을 어디까지 실행 했는 가를 알기 위한 것
     - Program Counter
     - 각종 register : 현재 어떤 값을 가지고 있었는 가 를 나타내게 됨
   - 프로세스의 주소 공간
     - code, data, stack
   - 프로세스 관련 커널 자료 구조 : 프로세스가 하나 생길때 마다 운영체제는 PCB를 하나씩 두면서 CPU 메모리를 얼마나 줘야할 지 관리한다.
     - PCB(Process Control Block)
     - Kernel stack

3. 프로세스의 상태

   ![process state](/assets/images/operating-system/process_state1.png)

   - Running
     - CPU 를 잡고 instruction 을 수행중인 상태
   - Ready
     - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족)
     - CPU를 받으면 프로그램이 바로 실행될 모든 준비가 된 상태
   - Blocked (wait, sleep)
     - CPU를 주어도 당장 instruction 을 수행할 수 없는 상태
     - Process 자신이 요청한 event (ex : I/O) 가 즉시 만족되지 않아 이를 기다리는 상태
   - New
     - 프로세스가 생성중인 상태
   - Terminated
     - 프로세스 수행이 끝난 상태

4. PCB (Process Control Block)

   - 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보	

     ![pcb](/assets/images/operating-system/pcb_block.png)

     (1) OS 가 관리상 사용하는 정보

     	- Process state - 프로세스 상태 (ready, 등 ), Process ID - 프로세스마다 고유 아이디
     	- scheduling information, priority

     (2) CPU 수행 관련 하드웨어 값

     - Program counter, registers

     (3) 메모리 관련

     - Code, data, stack 의 위치 정보

     (4) 파일 관련

     - Open file descriptors

5. 문맥 교환 (Context Switch)

   CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정

   CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행한다.

   - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB 에 저장 - 진행중이던 intruction 부터 실행을 재기하기 위해 
   - CPU를 새롭게 얻는 프로세스의 상태를 PCB 에서 읽어옴

   System call 이나 Interrupt 발생시 반드시 context switch 가 일어나는 것은 아님

   문맥 교환은 사용자 프로세스로 부터 운영체제로 CPU 가 넘어가는 것을 의미하는 게 아님

   사용자 프로세스로 부터 운영체제로 CPU가 넘어가서 다른 사용자 프로세스로 CPU를 주는 것이 문맥 교환이고 사용자 프로세스로 부터 운영체제로 CPU가 넘어가서 운영체제가 처리를 하고 다시 사용자 프로세스로 CPU를 돌려주는 것은 문맥교환이 아니다.

   ![context_switch](/assets/images/operating-system/context_switch.png)

   A에서 B 로 문맥 교환이 일어나게 되면 A 프로세스가 사용하던 캐쉬 메모리를 다 비워줘야 한다. 이 캐쉬 메모리를 비워주는 작업이 상당한 오버헤드이다. 하지만 1번 상황에선 캐쉬 메모리를 비울 필요가 없다.

6. 프로세스를 스케줄링하기 위한 큐

   - Job queue 

     현재 시스템 내에 있는 모든 프로세스의 집합

   - Ready queue

     현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합

   - Device queues

     I/O device 의 처리를 기다리는 프로세스의 집합

   ![queue](/assets/images/operating-system/process_queue.png)

7. 프로세스 스케쥴링 큐의 모습

   ![queue](/assets/images/operating-system/queue_state.png)

   1. 프로그램이 시작되면 레디큐에 들어가게 된다.
   2. 자기 차례가 되면 CPU를 얻는다
   3. CPU를 얻은 상태에서 할당 시간이 끝나거나 (timeout)면 다시 레디큐로 가서 줄을 선다
   4.  I/O 작업이 있으면 I/O 큐에 들어가서 줄을 서고 그 작업이 끝나면 다시 레디큐로 들어가서 줄을 선다.

8. 스케줄러 (Scheduler) 

   - Long-term scheduler (장기 스케줄러 or job scheduler) : 프로세스가 시작이 되어 ready 상태로 넘어 갈 때 메모리를 할당하는 것을 담당
     - 시작 프로세스 중 어떤 것들을 **ready queue** 로 보낼지 결정
     - 프로세스에 **memory**(및 각종 자원)을 주는 문제
     - degree of Multiprogramming (메모리에 올라가 있는 프로그램의 수) 을 제어
     - time sharing system 에는 보통 장기 스케줄러가 없음 (무조건 ready) -> 중기 스케줄러로 관리
   - Short-term scheduler (단기 스케줄러 or CPU scheduler) : 짧은 단위로 스케줄링이 이루어짐
     - 어떤 프로세스를 다음번에 **running** 시킬지 결정
     - 프로세스에 **CPU**를 주는 문제
     - 충분히 빨라야 함 (millisecond 단위)
   - Medium-term scheduler (중기 스케줄러 or Swapper) : 메모리에 너무 많은 프로그램을 올려두게 되어 공간이 부족하면 스와퍼가 일부 프로그램을 메모리에서 통째로 쫓아 낸다. 
     - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
     - 프로세스에게서 memory 를 뺏는 문제
     - degree of Multiprogramming 을 제어

9. 프로세스의 상태 (Process State) - 중기 스케줄러가 나타난 이후 메모리가 빼앗긴 상태를 이전의 상태로는 표시 할 수 없어 새로운 상태들이 생겨났다.

   ![process state](/assets/images/operating-system/process_state2.png)

   - Running

     - CPU를 잡고 instruction 을 수행중인 상태

   - Ready

     - CPU를 기다리는 상태 (메모리 등 다른 조건을 모두 만족하고 )

   - Blocked (wait, sleep)

     - I/O 등의 event 를 (스스로) 기다리는 상태

     **-> 자신이 요청한 event 가 만족이 되면 Ready**

   - Suspended (stopped)

     - 외부적인 이유로 프로세스의 수행이 정지된 상태

     - 프로세스는 통째로 디스크에 swap out 된다.

     - ex) 사용자가 프로그램을 일시 정지시킨 경우 (break key) 

     ​		시스템이 여러 이유로 프로세스를 잠시 중단 시킴 (메모리에 너무 많은 프로세스가 올라와 있을 때)

     **-> 외부에서 resume 해주어야 Active**

   







*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

