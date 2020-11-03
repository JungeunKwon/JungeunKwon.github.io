---
layout: post
title: "[운영체제] Memory Management 1"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Logical vs. Physical Address, 주소바인딩(Address Binding), Memory-Management Unit(MMU), Dynamic Relocation, Hadware Support for Address Translation, Some Treminologies, Dynamic Loading, Overlays, Swapping, Dynamic Linking, Allocation of Physical Memory, Contiguous Allocation"
featured: false
---

1. Logical vs Physical Address

   - Logical address(=virtual address) - 논리적 주소
     - 프로세스마다 독립적으로 가지는 주소 공간
     - 각 프로세스마다 0번지부터 시작
     - CPU가 보는 주소는 **logical address**임
   - Physical address - 물리적 주소
     - 메모리에 실제 올라가는 위치
   - 주소 바인딩 : 주소를 결정하는 것
     - Symbolic Address -> Logical Address -> Physical address

2. 주소 바인딩 ( Address Binding ) - 3가지 시점으로 나누어짐

   - Compile time binding - 컴파일 시에 이루어짐
     - 물리적 메모리 주소(physical address)가 컴파일 시 알려짐
     - 시작 위치 변경시 재컴파일
     - 컴파일러는 절대 코드(absolute code)생성
     - 메모리에 올라간 위치를 바꿀려면 재 컴파일 해야함
     - 논리적 주소 = 물리적 주소
   - Load time binding - 실행이 시작 될때
     - Loader 의 책임하에 물리적 메모리 주소 부여
     - 컴파일러가 재배치가능코드(relocateable code)를 생성한 경우 가능
   -  Execution time binding(=Run time binding) - 프로그램이 시작된 후에 주소가 바뀔 수 있을 때
     - 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
     - CPU가 주소를 참조할 때마다 binding 을 점검 (address mapping table)
     - 하드웨어적인 지원이 필요 ex ) base and limit registers,  MMU

3. Memory-Management Unit (MMU)

   - MMU(Memory-Management Unit)
     - logical address 를 physical address 로 매핑해 주는 Hardware device
   - MMU scheme
     - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register(= relocation register)의 값을 더한다.
     - relocation register(=base register) : 접근할 수 있는 물리적 메모리 주소의 최소값
     - Limit register : 논리적 주소의 범위
   - user program
     - logical address 만을 다룬다.
     - 실제 physical address를 볼 수 없으며 알 필요가 없다.

   ![mmu](/assets/images/operating-system/mmu.png)

   1. CPU가 논리 메모리 주소로 데이터 요청
   2. limit register로 메모리 주소가 범위 벗어나는 지 확인
   3. 벗어나면 trap 으로 에러 처리
   4. 안벗어나면 relocating 레지스터 값에 cpu가 요청한 논리 주소 더한 값 = 물리적 메모리주소

4. Some Terminologies

   - Dynamic Loading
   - Dynamic Linking
   - Overlays
   - Swapping

5. Dynamic Loading 

   - 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load 하는 것
   - memory utilization 의 향상
   - 가끔씩 사용되는 많은 양의 코드의 경우 유용
     - ex : 오류 처리 루틴
   - 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)
   - Loading : 메모리로 올리는 것

6. Overlays

   - 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림 - 다이나믹 로딩과 같음
   - 프로세스의 크기가 메모리보다 클 때 유용
   - 운영체제의 지원없이 사용자에 의해 구현
   - 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현

7. Swapping

   - 프로세스를 일시적으로 메모리에서 backing  store 로 쫓아내는 것
   - Backing store (=swap area)
     - 디스크 - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
   - Swap in / Swap out
     - 일반적으로 중기 스케줄러 (swapper)에 의해 swap out 시킬 프로세스 선정
     - priority-based CPU scheduling algorithm
       - priority 가 낮은 프로세스를 swapped out 시킴
       - priority 가 높은 프로세스를 메모리에 올려 놓음
     - Compile time 혹은 load time binding 에서는 원래 메모리 위치로 swap in 해야 함-> 비효율 적
     - Execution time binding 에서는 추후 빈 메모리 영역 아무 곳에서나 올릴 수 있음-> 효율적
     - swap time 은 대부분 transfer time (swap 되는 양에 비례하는 시간) 임 -> 방대한 양의 프로그램 자체가 한꺼번에 내려갔다가 올라오기 때문

8. Dynamic Linking

   - Linking 을 실행 시간 (execution time)까지 미루는 기법
   - Static linking
     - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
     - 실행 파일의 크기가 커짐
     - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비(ex. printf 함수의 라이브러리 코드)
     - 프로그램 A가 프린트 라이브러리를 포함하고 프로그램 B 가 프린트 라이브러리를 포함하면 각각 프린트 라이브러리 공간만큼 메모리가 필요하고 A의 프린트 라이브러리와 B의 프린트 라이브러리는 별도의 프로그램으로 된다. 
   - Dynamic linking
     - 라이브러리가 실행시 연결(link) 됨 - 라이브러리 호출되는 시점에
     - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub 이라는 작은 코드를 둠
     - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
     - 운영체제의 도움이 필요
     - 프로그램 A가 프린트 라이브러리를 포함하고 프로그램 B 가 프린트 라이브러리를 포함하면 A가 프린트 라이브러리를 실행해서 메모리에 올려두면 B는 추가로 올릴 필요 없이 올라와 있는 라이브러리를 공유해서 사용할 수 있다. (shared 라이브러리 라고 부름)

9. Allocation of Physical Memory

   - 메모리는 일반적으로 두 영역으로 나뉘어 사용
     - OS 상주 영역
       - interrupt vector 와 함께 낮은 주소 영역 사용
     - 사용자 프로세스 영역
       - 높은 주소 영역 사용
   - 사용자 프로세스 영역의 할당 방법
     - Contiguous allocation - 연속 할당, 프로그램이 메모리에 올라갈 때 통째로 올라가는 방법
       - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
       - Fixed partition allocation
       - Variable partition allocation
     - Noncontiguous allocation - 불연속 할당, 프로그램을 구성하는 주소공간을 잘게 쪼개서 메모리에 올리는 것
       - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
       - Paging - 같은 크기의 페이지 단위로 쪼개서 올리는 것
       - Segmentation
       - Paged Segmentation

10. Contiguous Allocation

    ![contiguous](/assets/images/operating-system/contiguous.png)

    고정 분할 방식 - 프로그램이 들어갈 사용자 영역의 메모리를 미리 partition으로 나누어 놓는 것 분할의 크기는 균일할 수도 가변적일 수도 있다.

    가변 불할 방식 - 프로그램이 실행될 때 마다 차곡차곡 메모리에 올려 두는 것

    - 고정 분할(Fixed partition) 방식

      - 물리적 메모리를 몇 개의 영구적 분할(partition)로 나눔
      - 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
      - 분할당 하나의 프로그램 적재
      - 융통성이 없음
        - 동시에 메모리에 load 되는 프로그램의 수가 고정됨
        - 최대 수행 가능 프로그램 크기 제한
      - Internal fragmentation 발생 (external fragmentation 도 발생)

    - 가변 분할(Variable partition) 방식

      - 프로그램의 크기를 고려해서 할당
      - 분할의 크기, 개수가 동적으로 변함
      - 기술적 관리 기법 필요
      - External fragmentation 발생

    - 프로그램을 메모리에 적재 시 낭비되는 메모리 조각 발생
      - External fragmentation (외부 조각)
        - 프로그램 크기보다 분할의 크기가 작은 경우
        - 아무 프로그램에도 배정되지 않은 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할
      - Internal fragmentation (내부 조각)
        - 프로그램 크기보다 분할의 크기가 큰 경우
        - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
        - 특정 프로그램에 배정되었지만 사용되지 않는 공간

    - Hole - 프로그램이 종료되면 생기는 공간

      - 가용 메모리 공간 ( 비어있는 메모리 공간 )
      - 다양한 크기의 hole 들이 메모리 여로 곳에 흩어져 있음
      - 프로세스가 도착하면 수용가능한 hole 을 담당
      - 운영체제는 다음의 정보를 유지 a) 할당 공간 b) 가용 공간(hole)

    - Dynamic Storage-Allocation Problem

      : 가변 분할 방식에서 size n 인 요청을 만족하는 가장 적절한 hole 을 찾는 문제

      - First-fit
        - Size 가  n 이상인 것 중 최초로 찾아지는 hole 에 할당
        - 리스트들을 다 탐색안해두 됨
      - Best-fit
        - Size 가 n 이상인 가장 작은 hole 을 찾아서 할당
        - Hole 들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole 의 리스트를 탐색해야함
        - 많은 수의 아주 작은 hole 들이 생성됨
      - Worst-fit
        - 가장 큰 hole 에 할당
        - 역시 모든 리스트를 탐색해야 함
        - 상대적으로 아주 큰 hole 들이 생성됨
      - First-fit 과 best-fit 이 worst-fit 보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐

    - Compaction

      - external fragmentation 문제를 해결하는 한 가지 방법
      - 사용 중인 메모리 영역을 한군데로 몰고 hole 들을 다른 한 곳으로 몰아 큰 block을 만드는 것
      - 적어도 런타임 바인딩이 지원되야아 가능함
      - 매우 비용이 많이 드는 방법임 
      - 최소한의 메모리 이동으로 compaction 하는 방법 (매우 복잡한 문제)
      - Compaction 은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다. 

    

    

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

