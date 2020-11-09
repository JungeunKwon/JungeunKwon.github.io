---
layout: post
title: "[운영체제] Disk Management and Scheduling"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Disk Structure, Disk Scheduling, Disk Management, Disk Scheduling Algorithm, FCFS(First Come First Service), SSTF(Shortest Seek Time First), SCAN, C-SCAN, Other Algorithms, Disk-Scheduling Algorithm의 결정,Swap-Space Management, RAID"
featured: false
---

1. Disk Structure

   - logical block
     - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
     - 주소를 가진 1차원 배열처럼 취급
     - 정보를 전송하는 최소 단위
   - Sector
     - Logical block이 물리적인 디스크에 매핑된 위치
     - Sector 0 은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다.

2. Disk Management

   - Physical formatting (Low-level formatting)
     - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
     - 각 섹터는 header + 실제 data(보통 512bytes) + trailer 로 구성
     - header 와 trailer 는 sector number, ECC (Error-Correcting Code)등의 정보가 저장되며  controller 가 직접 접근 및 운영
   - Partitioning
     - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
     - OS는 이것을 독립적 disk 로 취급 (logical disk)
     - 각각의 파티션을 파일 시스템이나 스왑에리아로 사용
   - Logical formatting - 파일 시스템으로 사용할 때
     - 파일 시스템을 만드는 것
     - FAT,inode, free space 등의 구조 포함
   - Booting - 파일 시스템에 있는 메모리가 올라 와서 부팅이 된다.
     - ROM 에 있는 "small bootstrap loader"의 실행
     - sector 0 (boot block)을 load 하여 실행 - 하드디스크에서 0번 섹터에 있는 내용을 메모리에 올리고 실행
     - sector 0 은 "full Bootstrap loader program" 
     - OS를 디스크에서 load 하여 실행 - 파일시스템에서 운영체제 커널의 위치를 찾아서 메모리에 올려서 실행

3. Disk Scheduling

   ![diskscheduling](/assets/images/operating-system/diskscheduling.png)

   - Access time 의 구성 - 디스크를 접근하는 access time
     - Seek time - 디스크가 읽고 쓰기를 요청한 트랙(실린더)로 이동하는데 걸리는 시간 디스크를 접근하는 시간에서 가장 큰 시간 구성 요소
       - 헤드를 해당 실린더로 움직이는데 걸리는 시간
     - Rotational latency - 원판이 회전해서 헤더한테 도착하는 데 걸리는 시간 시크타임 보다는 1/10 정도의 시간이 걸림
       - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
     - Transfer time - 디스크 데이터를 읽고 쓰는 시간 (굉장히 작은 시간 소요)
       - 실제 데이터의 전송 시간
     - 고속 입출력이 필요한 경우 한번의 seek 로 많은 양을 transfer 하면 이득, 매번 작은 데이터를 읽고 쓰기 하기 위해 seek 를 매번 해야하면 디스크가 굉장이 비효율적이기 때문

   - Disk bandwidth - 디스크의 성능을 나타내는 것
     - 단위 시간 당 전송된 바이트의 수
   - Disk Scheduling
     - seek time 을 최소화하는 것이 목표
     - Seek time = seek distance
     - 디스크 외부에서 디스크 요청이 들어 왔을 때 가능한 seek타임을 줄여서 디스크의 bandwidth 를 높여보자고 하는 것 -> disk scheduling
     - 요청들이 들어온 순서대로 들어오면 안쪽 트랙 바깥쪽 트랙 번갈아 가면서 요청이 들어오면 계속 헤드가 들락 날락 해야하기 때문에 비록 늦게 들어온 요청이더라도 안쪽에 있는 요청이 큐에 쌓여 있으면 그걸 먼저 처리하면 seek 타임을 줄일 수 있겠다는 것

4. Disk Scheduling Algorithm

   큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는 ? (실린더 위치는 0-199)

   98, 183, 37, 122, 14, 124, 65, 67

   - FCFS
   - SSTF
   - SCAN
   - C-SCAN
   - N-SCAN
   - LOOK
   - C-LOOK

5. FCFS(First Come First Service)

   ![fcfs2](/assets/images/operating-system/fcfs2.png)

   들어온 순서대로 처리해주는 알고리즘 -> 디스크 헤드가 상당히 많이 이동을 해야한다. 

6. SSTF (Shortest Seek Time First)

   ![sstf](/assets/images/operating-system/sstf.png)

   큐에 들어와 있는 요청 중 현재 헤드 위치에서 제일 가까운 요청을 먼저 처리한다. 

   디스크 헤드의 이동거리는 줄어들지만 starvation 의 문제점이 발생

   큐에 가까운 요청들이 계속 들어오면 멀리 있는 요청은 오래 기다려야 함

7. SCAN

   ![scan](/assets/images/operating-system/scan.png)

   디스크 스케쥴링 중 가장 간단하면서도 획기적인 방법(엘리베이터 스케줄링이라고도 부름)

   큐에 어떤 요청이 들어와 있던 상관 없이 가는 길목에 들어와 있는 요청을 처리 

   디스크 헤드의 이동 거리도 짧아지고 starvation 의 문제도 없음 비교적 공정 디스크의 이동거리 측면에서는 효율적

   가운데 있는 실린더와 가장자리에 있는 실린더의 기다리는 시간이 다르다. 

   가운데는 최악의 경우 반바퀴만 기다리면 되지만 가장자리는 한바퀴를 기다려야 한다.

8. C-SCAN

   ![cscan](/assets/images/operating-system/cscan.png)

   이동거리는 다소 길어질 수 있지만 큐에 들어온 요청들의 대기 시간이 균일하다. 

9. Other Algorithms

   - N-SCAN
     - SCAN의 변형 알고리즘
     - 일단 arm 이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job 은 되돌아올 때 service
   - LOOK and C-LOOK
     - SCAN 이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동 -> SCAN 과 C-SCAN 은 요청이 있던 없던 헤드는 끝까지 이동
     - LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동한다.

10. Disk-Scheduling Algorithm 의 결정

    - SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음
    - File 의 할당 방법에 따라 디스크 요청이 영향을 받음
    - 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS 와 별도의 모듈로 작성되는 것이 바람직하다.

11. Swap-Space Management

    - Disk 를 사용하는 두 가지 이유

      -  memory 의 volatile(휘발성) 한 특성 -> file system 지워지면 안되는 것은 영속성유지
      - 프로그램 실행을 위한 memory(D-RAM) 공간 부족 -> swap space (swap area) 물리적인 메모리는 한계가 있기 때문

    - Swap-space

      - Virtual memory system 에서는 디스크를 memory 의 연장 공간으로 사용
      - 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적
        - 공간 효율성 보다는 속도 효율성이 우선
        - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
        - 따라서, block의 크기 및 저장 방식이 일반 파일 시스템과 다름

      ![swapspace](/assets/images/operating-system/swapspace.png)

      file system 은 512 바이트의 섹터 단위로 데이터 저장, (데이터 넣는 방식은 연속, 리스트,인덱스 할당이 있었다. FAT 파일 방식등에서는 공간 효율성을 높이는 방식들을 사용)

      swap area 는 물리적인 메모리의 연장 공간이고 조금 있으면 다 사라지는 데이터들이기에 공간 효율성을 생각하지 않고 속도 효율성이 우선이다. 빨리 메모리에 올리고 내리고 해야해서 크기가 굉장히 큰 단위로 데이터를 올리고 내리고 한다. 

12. RAID
    - RAID (Redundant Array of Independent Disks)
      - 여러 개의 디스크를 묶어서 사용 (저렴한 디스크를 여러개 묶어서 사용하는 방법)
    - RAID 의 사용 목적
      - 디스크 처리 속도 향상
        - 여러 디스크에 block 의 내용을 분산 저장
        - 병렬적으로 읽어 옴 (interleaving, striping) - 기법 이름들
        - striping - 디스크를 조금 조금씩 나누어 조금조금씩 저장하는 방법
        - interleaving - 여기저기에서 동시에 조금씩 읽어오는 방법
        - 호스트 컴퓨터에서 데이터를 읽어 오라고 요청 했을 때, 여러군데에서 조금씩 조금씩 읽어 올 수 있다. 그러면 서로 협력을 하기 때문에 더 빠르다.
      - 신뢰성(reliability) 향상
        - 동일 정보를 여러 디스크에 중복 저장
        - 하나의 디스크가 고장(failure)시 다른 디스크에서 읽어옴(Mirroring, shadowing)
        - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다. 
        - parity - 오류가 생겼는 지 알아내고 복구할 수 있는 정도의 중복 저장만 아주 간략하게 해주는 기법

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

