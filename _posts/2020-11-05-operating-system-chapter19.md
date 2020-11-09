---
layout: post
title: "[운영체제] Virtual Memory"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Demand Paging, Memory에 없는 Page의 Page Table, Page Fault, Steps in Handling a Page Fault, Performance of Demand Paging, Free Frame이 없는 경우, Page Replacement, Optimal Algorithm, FIFO(First In First Out) Algorithm, LRU(Least Recently Used) Algorithm, LFU(Least Frequently Used) Algorithm, LRU와 LFU 알고리즘 예제, LRU와 LFU 알고리즘의 구현, 다양한 캐슁 환경, LRU와 LFU 알고리즘의 구현, Paging System에서 LRU, LFU 가능한가?, Clock Algorithm, Page Frame의 Allocation, Global vs. Local Replacement, Thrashing, Thrashing Diagram, Working-Set Model, Working-Set Algorithm, PFF(Page-Fault Frequency) Scheme, Page Size의 결정"
featured: false
---

1. Demand Paging

   - 실제로 필요할 때 page 를 메모리에 올리는 것
     - I/O양의 감소
     - Memory 사용량 감소
     - 빠른 응답 시간
     - 더 많은 사용자 수용
   - Valid / Invalid bit 의 사용
     - Invalid 의 의미
       - 사용되지 않는 주소 영역인 경우
       - 페이지가 물리적 메모리에 없는 경우
     - 처음에는 모든 page entry 가 invalid 로 초기화
     - address translation 시에 invalid bit 이 set 되어 있으면 -> "page fault" -> 요청한 페이지가 메모리에 없는 경우 이때 CPU는 자동적으로 운영체제로 넘어가서 운영체제가 요청한 페이지를 메모리에 올리게 된다.

2. Page Fault

   ![pagefault](/assets/images/operating-system/pagefault.png)

   - invalid page 를 접근하면 MMU(주소 변환을 해주는 하드웨어) 가 trap 을 발생시킴 (page fault trap)
   - Kernel mode 로 들어가서 page fault handler 가 invoke 됨
   - 다음과 같은 순서로 page fault 를 처리한다.
     - Invalid reference? (eg. bad address, protection violation) => abort process. 잘못된 요청인지 아닌지 (잘못된 주소, 접근 권한이 된 주소) 확인
     - Get an empty page frame. (없으면 뺏아온다: replace) 비어있는 페이지가 없으면 페이지 하나를 쫓아 낸다. 
     - 해당 페이지를 disk 에서 memory 로 읽어온다.
       - disk I/O 가 끝나기까지 이 프로세스는 CPU를 preempt 당함(block) -> 디스크에서 memory 로 올리는 과정은 꽤 오래 걸리기 때문에 disk I/O끝나기 전까지 다른 프로세스에게 CPU넘김
       - Disk read  가 끝나면 page tables entry 기록, valid/invalid bit = "valid" -> disk 에서 올리는 것 끝나면 CPU가 기록하고
       - ready queue에 process 를 insert -> dispatch later  페이지 요청했던 프로세스가 다시 CPU를 잡게 되어서 메모리 요청을 하면 이젠 page fault 가 안일어남
     - 이 프로세스가 CPU를 잡고 다시 running
     - 아까 중단되었던 instruction을 재개

3. Free frame 이 없는 경우 - page fault 에서 빈페이지가 없는 경우 페이지 하나를 쫓아내는 방법

   - Page replacement 
     - 어떤 frame 을 빼앗아 올지 결정해야 함
     - 곧바로 사용되지 않을 page 를 쫓아내는 것이 좋음
     - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음
   - Replacement Algorithm
     - page-fault rate 을 최소화하는 것이 목표 - 가능하면 페이지 폴트가 나지 않도록 하는 것이 목표
     - 알고리즘의 평가
       - 주어진 page reference string 에 대해 page fault 를 얼마나 내는지 조사
     - reference string 의 예 - 시간 순서에 따라 페이지가 참조된 순서 나열
       - 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

   ![pagereplace](/assets/images/operating-system/pagereplace.png)

   1. 어떤 것을 쫓아 낼지 희생양 (victim)을 고른다.
   2. 그 희생양이 메모리에 올라온 이후 내용이 변경되면 즉 write 발생했으면 쫓아낼 때 변경된 메모리 내용을 backing store 에 써줘야 한다. 변경된게 없으면 그냥 메모리에서 지워버리면 된다(이전 내용은 backing store 에 있기에)
   3. 쫓아낸 자리에 새로운 페이지를 올려둔다. 
   4. 쫓아낸 페이지의 페이지테이블의 비트를 인벨리드한다.
   5. 메모리에 올라온 페이지의 페이지테이블의 비트를 벨리드 하고 프레임 번호를 엔트리에 적어준다.

4. Optimal Algorithm 

   page falut 를 가장 적게 하는 알고리즘. 미래에 참조되는 page 를 미리 다 안다고 가정 (실제 시스템에 사용될 순 없음)

   Offline optimal algorithm 이라 부름   

   upper bound : 이 것 보단 성능이 좋을 수 없다는 뜻. 다른 알고리즘의 참고 사항이 된다는 뜻

   ![optimalalgorithm](/assets/images/operating-system/optimalalgorithm.png) 

5. FIFO (First In First Out) Algorithm

   ![fifo](/assets/images/operating-system/fifo.png)

   메모리에 먼저 들어온 페이지를 먼저 쫓아내는 방식

   FIFO Anomaly : 메모리 크기를 늘리면 페이지 폴트 수 가 더 늘어나는 현상이 발생 , 메모리 프레임을 늘렸는 데 성능이 나빠지는 것

6. LRU (Least Recently Used) Algorithm - 메모리 관리에서 제일 많이 쓰는 알고리즘

   ![lru](/assets/images/operating-system/lru.png)

   가장 덜 최근에 사용된(젤 오래 전에 사용된) 것을 먼저 쫓아내는 방식

   비교적 page faults rate를 줄일 수 있음

7. LFU (Least Frequently Used) Algorithm

   가장 덜 빈번하게 사용된 page 를 쫓아내는 것

   LFU : 참조 횟수(reference count) 가 가정 적은 페이지를 지움

   - 최저 참조 횟수인 page 가 여럿 있는 경우
     - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다.
     - 성능 향상을 위해 가장 오래 전에 참조된 page 를 지우게 구현할 수도 있다.
   - 장단점
     - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page 의 인기도를 좀 더 정확히 반영할 수 있음
     - 참조 시점의 최근성을 반영하지 못함
     - LRU보다 구현이 복잡함

8. LRU 와 LFU 알고리즘 예제

   ![lrulfu](/assets/images/operating-system/lrulfu.png)

   1. LRU 알고리즘에선 현재 시각 기준으로 1번 페이지가 젤 오래전에 사용 되었으므로 1번을 선택 -(단점) 마지막 참조 시점만 보기 때문에 그 이전에 어떤 기록을 가지고 있었는 지 생각하지 않는게 약점이다. 1번이 가장 오래전에 참조되긴 했지만 가장 많이 참조되었는 지는 고려하지 않는다. 
   2. LFU 알고리즘에선 4번 페이지가 젤 적게 참조 되었으므로 4번 선택 -(단점) 비록 4번이 젤 적게 참조 되었지만 이제 4번의 참조가 시작되고 있는 상황이라면 문제가 발생

9. LRU 와 LFU 알고리즘의 구현

   - LRU
     - 페이지들의 참조 시간순서에 따라 한줄로 줄 세움
     - 맨 위에 있는 것은 가장 오래전에 참조된 페이지
     - 링크드 리스트 형태로 참조 시간순으로 관리
     - 이미 참조된 페이지가 또 참조되면 맨 뒤로 보낸다.
     - 쫓아낼 때는 맨 위에 있는 것을 쫓아낸다.
     - 시간 복잡도 O(1) 시간의 비교를 할 필요가 없기 때문(맨 위에꺼만 쫓아내면 되서)
   - LFU
     - 가장 참조 횟수가 적은 페이지가 맨 위에
     - LFU 알고리즘은 참조 횟수가 한번 늘어 났다고 해서 가장 많이 참조된 것이 아니기 때문에 맨 뒤로 보낼 수 없다. 
     - 참조 되었던 애가 또 참조가 되면 참조 횟수를 한번 늘리고 자기 뒤애 줄서있는 애들이랑 참조 횟수를 비교 해야한다.
     - 그래서 LRU 처럼 링크드로 줄세우기로 구현을 하면 O(n) 의 시간 복잡도가 필요하다.
     - Heap 으로 구현을 하게 되면 O(log n)의 시간 복잡도가 걸리게 된다. 
     - 참조 되었던 애가 또 참조가 되면 자식 두개중 하나만 비교하면 되기 때문
     - 쫓아 낼 때는 root 를 쫓아내면 됨

10. 다양한 캐슁 환경

    - 캐슁 기법
      - 한전된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스 하는 방식, 캐쉬에 저장해두고 다음에 똑같은 요청이 왔을 때 느린 저장장치로 가지 않고 캐쉬로 부터 바로 서비스를 하면 더 빠르다. 
      - paging system 외에도 cache memory, buffer caching, Web caching 등 다양한 분야에서 사용, paging 시스템에서 메인 메모리가 한정된 저장장치 backing store 가 느린 저장장치 (빠른 곳 - 물리적 메모리, 느린 곳 - disk)
    - 캐쉬 운영의 시간 제약 - 어떤 걸 쫓아낼 지 고르는 알고리즘에서 너무 오래 걸리면 안된다
      - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
      - Buffer caching 이나 Web caching 의 경우
        -  O(1) 에서 O(log n) 정도까지 허용
      - Paging system인 경우
        - page fault 인 경우에만 OS 가 관여함
        - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음
        - O(1) 인 LRU의 list 조작조차 불가능

11. Paging System 에서 LRU, LFU 가능한가?

    ![lrulfu2](/assets/images/operating-system/lrulfu2.png)

    1. CPU 에서 프로세스 A 가 실행중이면 매 시간마다 프로세스 A 의 논리 메모리에서 인스트럭션을 하나씩 읽어 와서 실행한다. 
    2. CPU 에서 프로세스 A 에 대한 논리 주소를 주면 페이지 테이블을 통해 물리적인 메모리로 변환을 해서 물리적인 메모리의 내용을 CPU로 읽어 들인다. 
    3. 만약 주소 변환을 했는 데 물리적인 내용이 있으면 물리 메모리의 내용을 CPU로 가져간다.
    4. 이 주소 변환하는 과정은 하드웨어가 하는 일 (운영체제는 하는 일 없음)
    5. 근데 주소 변환 시도 했는데 invalid 라서 물리적인 메모리주소에 페이지가 없으면 page fault 발생 -> 디스크에서 페이지를 읽어와 물리 메모리로 올려야 하기 때문에 디스크 I/O 발생 -> 이 작업은 프로세스 A 가 할 수 없는 일 -> trap 이 발생 -> CPU제어권이 프로세스에서 운영체제로 넘어감
    6. 운영체제가 디스크에 있는 페이지를 물리적 메모리에 올려둔다. 그 과정에서 물리 메모리가 꽉 차있으면 어느 하나를 쫓아내는 리플레이스 먼트를 해야한다. 어떤 걸 쫓아낼지 운영체제가 정해야 하는데 LRU 알고리즘을 쓸려면 가장 오래전에 참조된 애를 찾아내야 하고 LFU 알고리즘을 사용하면 가장 참조 횟수가 적은 애를 찾아내야한다.
    7. 과연 운영체제가 가장 오래전에 참조된 애와 가장 참조 횟수가 적은 애를 알아낼 수 있는가?
    8. 답은 알 수가 없다. 프로세스가 요청한 페이지가 물리 공간에 있으면 운영체제한테 CPU가 안넘어 온다. 하드웨어적으로 그냥 물리 공간에 내용을 읽어 들인다.  반면에 page fault 가 나면 CPU가 운영체제로 가고 disk 에 있던 페이지가 메모리에 올라온 시간을 알 수 있다. 
    9. 즉 Paging System 에서는 운영체제한테 주어지는 정보가 반쪽만 주어진다. 
    10. 즉 LRU 나 LFU 같은 알고리즘의 경우 Paging System 즉 버츄얼 메모리 시스템에선 사용할 수 없다. 버퍼 캐싱이나 웹 캐싱에서는 사용되는 알고리즘

12. Clock Algorithm - paging system 에서는 쫓아내는 것을 정하기 위해 Clock 을 사용

    ![clock](/assets/images/operating-system/clock.png)

    각각의 사각형이 페이지 프레임 , 즉 물리적인 메모리안에 들어 있는 페이지

    하드웨어는 그 페이지가 참조가 되면 reference bit 을 1로 셋팅해 준다.

    page fault 발생 시 reference bit을 참조하여 이 1이면 최근에 참조 되었다는 뜻이므로 0으로 바꾸고 다음 비트로 넘어간다. 0이면 그 페이지를 쫓아낸다.

    즉 reference bit 이 1이라는 말은 시계가 한 바퀴 돌 동안 참조 되었다는 뜻이고 0이라는 말은 시계가 한 바퀴 돌 동안 참조 되지 않았다는 뜻

    - LRU의 근사 (approximation) 알고리즘
    - 여러 명칭으로 불림
      - Second chance algorithm - 기회를 한번 더 준다
      - NUR (Not Used Recently) 또는 NRU (Not Recently Used) - 가장 최근에 사용되지 않은 페이지
    - Reference bit 을 사용해서 교체 대상 페이지 선정 (circular list)
    - reference bit 가  0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
    - 포인터 이동하는 중에 reference bit 1 은 모두 0으로 바꿈
    - Reference bit 이 0인 것을 찾으면 그 페이지를 교체
    - 한 바퀴 되돌아와서도(=second chance) 0이면 그때에는 replace 당함
    - 자주 사용되는 페이지라면 second chance 가 올 때 1
    - Clock algorithm 의 개선
      - reference bit 과 modified bit (dirty bit)을 함께 사용
      - reference bit = 1 : 최근에 참조된 페이지
      - modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지) - 어떤 페이지가 메모리에서 wirte 가 발생 시 ,  해당 페이지가 메모리에서 쫓겨날 때 modified bit 이 1이면 backing store 에서 올라온 뒤 데이터가 변경이 되었다는 뜻이므로 backing store  에 수정된 내용을 반영한 다음에 쫓아내야 하고 modified bit 이 0이면 변경이 안되었다는 뜻으로 이미 똑같은 게 backing store 에 있으므로 그냥 쫓아내면 된다. 

13. Page Frame 의  Allocation

     LRU, LFU, Clock 알고리즘에서는 프로그램 여러개가 물리적인 메모리에 같이 올라와 있고 쫓아낼 때는 어떤 프로세스에 속한 메모리인지 무관하게 쫓아냄. 

    실제로는 어떤 프로그램이 원활하게 실행 되기 위해서는 (어떤 프로그램이 실행되면서 page fault 를 잘 안내고 실행이 되려면 ) 일련의 페이지들이 메모리에 같이 올라와 있어야지만 더 효율적

    ex. 어떤 프로그램이 for 문을 돌고 for 문에 속하는 페이지가 3개다 그러면 3개의 페이지를 주면 page fault 가 안난다. 그런데 페이지를 2개만 주면 for 문이 반복될 때마다 계속 page fault 가 발생한다. 

    ex. 어떤 프로그램이 실행되면서 code 에 해당되는 명령어 뿐만아니라 data 에도 접근을 한다면   최소한 code 에 해당하는 페이지와 data 에 해당하는 페이지가 메모리에 같이 올라와 있어야 한다.

    즉 프로그램마다 적어도 몇개의 페이지를 줘야지만 page fault 가 잘 안난다.

    또한, 프로그램 별로 page allocation 해주지 않으면 특정 프로그램이 페이지 프레임을 다 장악해서 다른 프로그램의 page 들을 다 쫓아낼 수 도 있음.

    - Allocation problem :  각 process 에 얼마만큼의 page frame 을 할당할 것인가?
    - Allocation 의 필요성 - 각각의 프로그램한테 어느 정도의 page frame 을 나눠 주자는 것
      - 메모리 참조 명령어 수행시 명령어, 데이터 등 여러 페이지 동시 참조
        - 명령어 수행을 위해서 최소한 할당 되어야 하는 frame 의 수가 있음
      - Loop 를 구성하는 page 들은 한꺼번에 allocate 되는 것이 유리함
        - 최소한의 allocation 이 없으면 매 loop 마다 page fault
    - Allocation Scheme
      - Equal allocation : 모든 프로세스에 똑같은 갯수 할당 - 어떤 프로그램은 페이지가 많이 필요하고 어떤 프로그램은 적게 필요한 상황에서 비효율적
      - Proportional allocation : 프로세스 크기에 비례하여 할당 - 같은 프로그램이라 하더라도 시간에 따라 필요한 페이지의 개수가 다를 수 있음
      - Prioirty allocation : 프로세스의 priority 에 따라 다르게 할당 - CPU 우선 순위가 높은 프로그램한테 더 많이 할당
    - 프로그램마다 필요로 하는 페이지를 미리 할당해주고 그렇게 해서 page fault 가 적게 나게 하겠다는 방법

14. Global VS Local Replacement -> 할당을 해주지 않더라도 replacement 알고리즘을 사용하다 보면 알아서 어느 정도 page 가 할당이 됨

    - Global replacement - 굳이 미리 할당하지 않고 알아서 알고리즘을 사용하면 그때 그때 프로스 별로 메모리 할당량이 자동으로 조절 되기 때문에 미리 할당하는 방법을 쓰지 않겠다는 방법 **다른 프로그램의 페이지도 쫓아낸다.**
      - Replace 시 다른 process 에 할당된 frame 을 빼앗아 올 수 있다.
      - Process 별 할당량을 조절하는 또 다른 방법임
      - FIFO, LRU, LFU 등의 알고리즘을 global replacement 로 사용시에 해당
      - Working set, PFF 알고리즘 사용 - 할당의 효과가 있는 알고리즘들
    - Local replacement - 미리 메모리 할당을 하는 것 **자신에게 할당된 페이지 프레임내에서만 쫓아낸다**
      - 자신에게 할당된 frame 내에서만 replacement
      - FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영시 

15. Thrashing  -프로그램한테 페이지가 적게 할당이 되어 page fault가 지나치게 자주 발생하여  전체 시스템이 페이지 fault 가 자주 발생하는 것

    ![trashing](/assets/images/operating-system/trashing.png)

    메모리에 프로그램 수가 적으면 CPU 사용률이 낮다.
    메모리에 프로그램 수를 늘릴 수록 CPU 사용률이 높다.

    그런데 계속해서 올려주게 되면 thrashing 이 발생하게 되면서 CPU 사용률이 똑 떨어진다. 이걸 막기 위해선 MPD를 조절 해주어야 한다.

    - 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우 발생
    - Page fault rate 이 매우 높아짐
    - CPU utilization 이 낮아짐 - CPU 가 인스트럭션을 실행할려 하면 페이지가 없어서 I/O발생하고 다른 프로그램으로 넘어가서 인스트럭션을 실행할려 하는데 또 없어서 또 I/O발생
    - OS 는 MPD (Multiprogramming degree)를 높여야 한다고 판단
    - 또 다른 프로세스가 시스템에 추가됨 (higher MPD)
    - 프로세스 당 할당된 frame 의 수가 더욱 감소
    - 프로세스는 page 의 swap in / swap out 으로 매우 바쁨
    - 대부분의 시간에 CPU는 한가함 
    - low throughput

16. Working - Set Model  -> 프로그램들이 메모리에서 원활하게 실행 될려면 어느정도 페이지를 가져야 한다는 것

    - Locality of reference

      - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다. ex) for 문이 실행되면 그 루프만 구성하는 페이지만 집중적으로 참조되고 함수를 실행하면 그 함수를 구성하는 페이지만 집중적으로 참조된다. 
      - 집중적으로 참조되는 해당 page 들의 집합을 locality set 이라 한다. 

    - Working-set Model - 메모리에 한꺼번에 올라와 있는 것을 보장해주는 기법

      - Locality 에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page 들의 집합을 Working Set 이라 정의한다.
      - Working Set 모델에서는 process 의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame 을 반납한 후 swap out (suspend) - 페이지가 5개 필요한데 3개만 할당 받으면 전체를 반납하고 디스크로 스왑 아웃 해버린다. ( 워킹 셋이 한꺼번에 다 올라가는 게 보장이 안되면 뺴버린다 )
      - Thrashing 을 방지함
      - Multiprogramming degree 를 결정함

    - Working set 의 결정

      - Working set window 를 통해 알아냄

      ![workingset](/assets/images/operating-system/workingset.png)

      과거 델타 시간동안 참조된 페이지들을 워킹셋이라 파악하고 쫓아내지 않는다. 델타 시간 이후에는 버린다. 

    - Working-Set Algorithm
      - Process들의 working set size 의 합이 page frame 의 수보다 큰 경우
        - 일부 process 를 swap out 시켜 남은 process 의 working set을 우선적으로 충족시켜준다.(MPD(멀티프로그래밍디그리)를 줄임)
      - Working set을 다 할당하고도 page frame 이 남는 경우
        - Swap out 되었던 프로세스에게 working set 을 할당(MPD를 키움)
    - Window size △
      - Working set 을 제대로 탐지하기 위해서는 window size 를 잘 결정해야 함
      - △값이 너무 작으면 locality set 을 모두 수용하지 못할 우려
      - △값이 크면 여러 규모의 locality set 수용
      - △값이 무한대이면 전체 프로그램을 구성하는 page 를 working set 으로 간주 
    
17. PFF (Page-Fault Frequency) Scheme

    ![PFF](/assets/images/operating-system/PFF.png)

    MPD를 조절하면서 스래슁을 방지하는 알고리즘

    워킹셋 처럼 워킹셋 추정하는 것이 아니라 직접 페이지 폴트 레이트를 본다. 현재 시점에 페이지 폴트가 얼마나 나고 특정 프로그램이 페이지폴트를 얼마나 내는지를 본다. 만약에 특정 프로그램이 페이지 폴트를 많이 낸다 -> 워킹셋이 메모리에 다 보장 안된 상태이기 때문에 페이지를 더 준다. 페이지 폴트를 너무 발생 안한다 -> 쓸데없이 메모리를 많이 가지고 있다고 생각해서 페이지를 좀 빼앗는다. 이렇게 일정한 페이지폴트를 유지하게 하는 방법

    페이지를 더 줘야하는데 남아있는 페이지가 없으면 그 프로그램을 통째로 swap out 시킨다.

18. Page Size 의 결정
    - Page size 를 감소시키면
      - 페이지 수 증가
      - 페이지 테이블 크기 증가
      - Internal fragmentation 감소
      - Disk transfer 의 효율성 감소
        - Seek/rotation vs transfer ->  disk 의 seek 시간이 오래걸리기 때문에 한번 Seek 했을 때 가능한 많은양의 뭉치를 메모리에 올리는 게 효율적이다.
      - 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
        - Locality의 활용 측면에서는 좋지 않음
    - Trend
      - Larger page size







*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

