---
layout: post
title: "[운영체제] Deadlock"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "교착상태(deadlock), The Deadlock Problem, Deadlock 발생의 4가지 조건, Resource-Allocation Graph(자원할당그래프), Deadlock Prevention, Deadlock의 처리 방법, Deadlock Avoidance, Resource Allocation Graph algorithm, Banker's Algorithm, Example of Banker's Algorithm"
featured: false
---

1. 교착상태(deadlock)

   - 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 된 상태- 

   - Resource(자원)

     - 하드웨어, 소프트웨어 등을 포함하는 개념
     - ex ) I/O device, CPU cycle, memory space, semaphore 등
     - 프로세스가 자원을 사용하는 절차
       - Request(자원 요청) , Allocate(자원 획득) , Use(자원 사용), Release(자원 반납)

   - Deadlock Example 1

     - 시스템에 2개의 tape drive 가 있다.
     - 프로세스 P1 과 프로세스 P2 각각이 하나의 tape drive 를 보유한 채 다른 하나를 기다리고 있다.

   - Deadlock Example 2

     - Binary semaphores A and B

       | P0   | P1   |
       | ---- | ---- |
       | P(A) | P(B) |
       | P(B) | P(A) |

2. Deadlock 발생의 4가지 조건 

   - Mutual exclusion (상호 배제)
     
   - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
     
   - No preemption (비 선점)

     - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

   - Hold and wait (보유대기)

     - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음

   - Circular wait (순환대기)

     - 자원을 기다리는 프로세스간에 사이클이 형성 되어야 함

     - 프로세스 P0, P1, ... Pn이 있을 때

       P0은 P1이 자원을 기다림

       P1은 P2이 자원을 기다림

       Pn-1은 Pn이 자원을 기다림

       Pn은 P0이 자원을 기다림

   - 4가지 중 어느 하나를 만족하지 않으면 데드락은 발생하지 않는다.

3. Resource-Allocation Graph (자원할당그래프)

   ![resourcegraph](/assets/images/operating-system/resourcegraph.png)

   데드락이 발생했는 지를 알아보기 위한 그래프

   ○ : 프로세스 □ : 자원 

   자원 -> 프로세스 : 이 자원이 이 프로세스에 속해있다. 이 프로세스가 이 자원을 가지고 있다.

   프로세스 -> 자원 : 이 프로세스가 이 자원을 요청했다. (아직 획득하지는 못함)

   사각형안에 동그라미 :  자원의 인스턴스 수 (동그라미 중 아무거나 하나 가져가면 됨)

   ![resourcegraph2](/assets/images/operating-system/resourcegraph2.png)

   자원당 인스턴스가 하나밖에 없으면 사이클은 곧 데드락이다.

   자원당 인스턴스가 여러개인 상황이면 데드락일 수 도 있고 아닐 수 도 있다.

4. Deadlock의 처리 방법

   - Deadlock Prevention 
     - 자원 할당 시 Deadlock의 4 가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
   - Deadlock Avoidance 
     - 자원 요청에 대한 부가적인 정보를 이용해서 deadlock 의 가능성이 없는 경우에만 자원을 할당
     - 시스템 state 가 원래  state 로 돌아올 수 있는 경우에만 자원 할당
   - Deadlock Detection and recovery
     - Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock발견시 recover
   - Deadlock Ignorance
     - Deadlock 을 시스템이 책임지지 않음
     - UNIX를 포함한 대부분의 OS가 채택
   - 1,2 번은 데드락이 생기지 않게 미연에 방지
   - 3번은 데드락이 생기도록 놔두고 시스템이 느려지면 데드락이 있는지 detection 한 후 데드락이 있으면 recovery 
   - 4번은 데드락에 대해 아무일도 하지 않는다. - 데드락이 생기든 말든 관여 x , 운영체제가 아무일도 안하면 사람이 프로세스를 죽이거나 한다. 데드락은 빈번히 발생하는 이벤트가 아니기때문에 그것을 미연에 방지하기 위해 훨씬 더 많은 오베헤드를 들이는 것은 현대적인 시스템에서 비효율적.

5. Deadlock Prevention

   - Mutual Exclusion 
     - 공유해서는 안되는 자원의 경우 반드시 성립해야 함 - 배제불가
   - Hold and wait
     - 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다.
     - 방법 1. 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법 - 모든 걸 hold 하게 함 프로세스 끝날때 까지 자원을 hold 하고 있어서 비효율적
     - 방법 2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청 - 모두 다 얻거나 아무것도 가지지 않거나 하는 상태 
     - 자진해서 반납함으로써 문제 해결
   - No preemption
     - process 가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
     - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다.
     - State 를 쉽게 save 하고 restore 할 수 있는 자원에서 주로 사용 (CPU, memory) - 중간에 빼앗아 오면 하던일이 엉망이 되는 경우는 사용하기 어려움
   -  Circular Wait
     - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
     - 예를 들어 순서가 3 인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj을 할당받기 위해서는 우선 Ri를 release 해야 한다. 
   - -> Utilization 저하, throughput 감소, starvation 문제
   - 자원의 이용률이 낮아지고 시스템의 성능이 나빠지고 기아현상이 발생할 수 있음. 생기지도 않을 데드락에 제약조건을 많이 달아놓음으로써 비효율적임

6. Deadlock Avoidance - 프로세스가 시작될 때 프로세스가 종료될때까지 사용할 자원의 최대량을 미리 알고 있다고 가정

   - 자원 요청에 대한 부가정보를 이용해서 자원 할당이 deadlock 으로 부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당

   - 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법 임

   - safe state

     - 시스템 내의 프로세스들에 대한 safe  sequence가 존재하는 상태

   - safe sequence

     - 프로세스의 sequence <P1, P2, ..., Pn>이 safe 하려면 P1(i <= i <= n)의 자원 요청이 **"가용 자원 + 모든 Pj(j < i)의 보유 자원"**에 의해 충족되어야 한다.
     - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
       - Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj(j < i )가 종료될 때 까지 기다린다.
       - Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다.

     ![deadlockavoidance](/assets/images/operating-system/deadlockavoidance.png)

   - 시스템이 safe state 에 있으면 no deadlock
   - 시스템이 unsafe state에 있으면 possibility of deadlock
   - Deadlock avoidance
     - 시스템이 unsafe state에 들어가지 않는 것을 보장
     - 2가지 경우의 avoidance 알고리즘
       - Single instance per resource types
         - Resource Allocation Graph algorithm 사용
       - Multiple instances per resource types
         - Banker's Algorithm 사용

7. Resource Allocation Graph algorithm

   ![resourcegraph3](/assets/images/operating-system/resourcegraph3.png)

   점선 화살표 : 이 프로세스가 적어도 한번은 해당 자원을 사용할 일이 있다. 

8. Example of Banker's Algorithm

   - 가정

     - 모든 프로세스는 자원의 최대 사용량을 미리 명시
     - 프로세스가 요청 자원을 모두 할당받은 경우 유한 시간 안에 이들 자원을 다시 반납한다.

   - 방법

     - 기본 개념 : 자원 요청시 safe 상태를 유지할 경우에만 할당
     - 총 요청 자원의 수가 가용 자원의 수보다 적은 프로세스를 선택 (그런 프로세스가 없으면 unsafe 상태)
     - 그런 프로세스가 있으면 그 프로세스에게 자원을 할당
     - 할당받은 프로세스가 종료되면 모든 자원을 반납
     - 모든 프로세스가 종료될 때까지 이러한 과정 반복

     ![banker's](/assets/images/operating-system/banker's.png)

     총 자원에서 현재 할당된 자원의 개수를 빼면 가용 자원(Available)

     Max 는 프로세스가 시작할 때 자원의 최대 개수를 미리 구해둔 것

     Need 는 최대 자원에서 지금 할당 받은 자원의 개수를 뺀 것

     가용 자원으로 추가요청(최대 요청)을 만족할 수 있는 프로세스에게만 할당

     할당된 프로세스가 끝나면 자원 반환 -> 가용 자원이 늘어남

     자원이 남아 있는 데도 주지 않아서 비효율적  

     ![banker's2](/assets/images/operating-system/banker's2.png)

9. Deadlock Detection and recovery

   - Deadlock Detection

     - Resource type 당 single instance 인 경우
       - 자원할당 그래프에서의 cycle 이 곧 deadlock을 의미
     - Resource type 당 multiple instance인 경우
       - Banker's algorithm과 유사한 방법 활용

   - Wait-for graph 알고리즘

     ![waitfor](/assets/images/operating-system/waitfor.png)

     자원당 인스턴스가 한개인 경우

     자원할당 그래프에서 자원들을 빼고 프로세스들만 연결 

     프로세스1 - > 프로세스 2 : 프로세스 1은 프로세스 2가 가진 자원을 기다리고 있다.

     그래프에서 싸이클을 찾는다. 

     - Resource type 당  single instance 인 경우
     - Wait-for graph
       - 자원할당 그래프의 변형
       - 프로세스만으로 node 구성
       - Pi가 가지고 있는 자원을 Pk 가 기다리는 경우 Pk -> Pi
     - Algorithm
       - Wait-for graph 에 사이클이 존재하는지를 주기적으로 조사
       - O(n^2)

     ![deadlockdetection](/assets/images/operating-system/deadlockdetection.png)

     자원당 인스턴스가 여러개인 경우

     Allocation 현재 할당된 자원

     데드락 디텍션은 프로세스가 요청하면 바로 자원을 줘서 프로세스가 최대 얼마 필요한지는 알 필요가 없다. 

     요청이 왔지만 가용자원이 없기 때문에 무시

     현재 요청된 자원이 없는 프로세스에서 할당된 자원이 반납 될 것이라는 것을 가정->가용 자원에 추가 

     그  가용자원으로 추가 요청 처리함 -> 추가 요청으로 자원을 얻은 자원은 또 반납할 것이라고 가정 -> 가용자원에 추가

   - Recovery
     - Process termination - 프로세스를 종료시키는 방법
       - Abort all deadlocked processes - 데드락에 연류된 모든 프로세스들을 한꺼번에 죽이는 것
       - Abort one process at a time until the deadlock cycle is eliminated - 데드락이 없어질 때 까지 데드락에 연류된 프로세스들을 하나씩 죽이는 것
     - Resource Preemption - 프로세스로 부터 자원을 뺐는 것
       - 비용을 최소화할 victim 의 선정
       - safe state 로 rollback 하여 process를 restart
       - Starvation 문제
         - 동일한 프로세스가 계속해서 victim 으로 선정되는 경우
         - cost factor 에  rollback 횟수도 같이 고려

10. Deadlock Ignorance

    - Deadlock 이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음
      - Deadlock이 매우 드물게 발생하므로 deadlock에 대한 조치 자체가 더 큰 overhead일 수 있음
      - 만약, 시스템에 deadlock 이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 process 를 죽이는 등의 방법으로 대처
      - UNIX, Windows 등 대부분의 범용 OS가 채택

    

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

