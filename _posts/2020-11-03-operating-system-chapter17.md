---
layout: post
title: "[운영체제] Memory Management 2"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Paging, Dynamic Relocation, Paging Example, Address Translation Architecture, Implementation of Page Table, Paging Hardware with TLB, Associative Register, Effective Access Time, Two-Level Page Table, Address-Translation Scheme, Two-Level Paging Example,Multilevel Paging and Performance, Two-Level Page Table, Valid (v)/ Invalid (i) Bit in a Page Table, Memory Protection, Inverted Page Table, Inverted Page Table Architecture, Shared Page, Shared Pages Example"
featured: false
---

1. Paging

     ![paging](/assets/images/operating-system/paging.png)

   - process 의 virtual memory 를 동일한 사이즈의 page 단위로 나눔
   - virtual memory 의 내용이 page 단위로 noncontiguous 하게 저장됨
   - 일부는 backing storage에, 일부는 physical memory 에 저장
   - Basic Method
     - Physical memory 를 동일한 크기의 frame 로 나눔
     - logical memory 를 동일 크기의 page 로 나눔 (frame 과 같은 크기)
     - 모든 가용 frame 들을 관리
     - page table 을 사용하여 logical address 를 physical address 로 변환
     - External fragmentation 발생 안함
     - Internal fragmentation 발생 가능

2. Implementation of Page Table

   - Page table 은 main memory 에 상주
   - **Page-table base register (PTBR)**가 page table 을 가리킴 - 메모리상의 페이지 테이블이 어디 있는 지 시작 위치를 가르킴
   - **Page-table length register (PTLR)** 가 테이블 크기를 보관 - 페이지 테이블의 길이를 알려줌
   - 모든 메모리 접근 연산에는 2번의 memory access 필요
   - page table 접근 1번, 실제 data/instruction 접근 1번
   - 속도 향상을 위해 associative register 혹은 translation look-aside buffer **(TLB)**라 불리는 고속의 lookup hardware cache 사용 - 메인 메모리와 CPU 사이에 위치하는 주소 변환해주는 계층

3. Paging Hardware with TLB

     ![TLB](/assets/images/operating-system/TLB.png)

     원래는 cpu 가 주는 논리 주소에 대해서 (물리)메모리상에 있는 page table을 통해서 주소 변환을 하고 그 변환된 주소로 물리적인 메모리에 접근한다. 이렇게 되면 두번의 메모리 접근으로 속도가 느려 속도향상을 하기 위해 TLB라는 별도의 하드웨어를 둔다. 

     TLB : 메모리의 주소 변환을 위한 별도의 캐쉬 메모리 - 데이터를 위한 캐쉬 메모리와는 조금 다른 개념 page table 에서 빈번히 참조되는 일부 엔트리를 캐슁하고 있음.

     cpu 가 논리 주소를 주면 page table 접근 전에 TLB 를 먼저 검색해 본다. 혹시 이 주소 변환 정보 중에서 TLB로 변환이 가능한지 먼저 체크. TLB 에 저장되어 있으면 TLB를 통해 바로 주소 변환이 이루어지고 메모리에 접근한다. 즉 메모리를 한번만 접근한다. TLB 에 없는 경우 page table 을 통해서 일반적인 주소 변환을 한다. 

     page table 과 TLB 의 차이점은 page table은 p번째 엔트리를 주소변환 하면 f 라는 것이 바로 나오는데 TLB 는 그 엔트리를 모두 저장하고 있는 것이 아니기 때문에 p번째 엔트리를 주소 변환하면  무엇인지에 대한 정보까지 담고 있어야 한다. 즉 p(논리적인 주소 변호) ,f(p에 대한 주소 변환된 프레임) 쌍으로 담고 있어야 한다. 

     TLB 에서는 p 를 찾기 위해서는 TLB 전체를 탐색해야 한다. page table 에서는 p를 찾을려면 인p번째 인덱스에 접근하면 된다. -> 탐색 시간에 차이가 있다.

     *캐쉬메모리 : 메인 메모리에서 빈번히 사용되는 데이터를 캐쉬 메모리에 저장해둬서 CPU로 부터 더 빨리 접근할 수 있게 해주는 것

4. Associative Register

   - Associative registers (TLB) : parallel search 가 가능 -> TLB에는 page table 중 일부만 존재 하기 때문에 p 를 검색하기 위해서는 TLB 전체를 검색해야 한다. 검색 속도를 향상 시키기위해 병렬 검색이 가능하게 해준다.
   - Address translation
     - page table 중 일부가 associative register 에 보관되어 있음
     - 만약 해당 page 가 associative register 에 있는 경우 곧바로 frame 을 얻음
     - 그렇지 않은 경우 main memory 에 있는 page table 로 부터 frame 을 얻음
     - TLB 는 context switch 때 flush(remove old entries) -> 프로세스 마다 page table과 tlb가 존재하기 때문

   ![accesstime](/assets/images/operating-system/accesstime.png)

   - TLB 접근 시간 (입실론)

   - 메인 메모리 접근 시간 1 

   - TLB 로 부터 주소 변환이 되는 비율 (알파)

   - 결론 : TLB 로 부터 주소 변환 되는 비율 알파가 굉장히 높고 입실론은 굉장이 작은 값이므로  페이지 테이블만 있을 때 접근한 시간 2보다는 작은 값이 된다는 뜻

5. Two-Level Page Table

   ![twolevelpagetable](/assets/images/operating-system/twolevelpagetable.png)

   cpu가 논리 적인 주소를 주게되면 페이지 테이블을 두단계 거쳐서 주소 변환을 하고 메모리에 접근 한다. 

   속도는 줄어 들지 않음 -> 페이지테이블 두번 접근으로 시간은 더 느려지지만 페이지 테이블을 위한 공간이 줄어든다. 그게 2단계 페이지 테이블의 목적

   - 현대의 컴퓨터는 address space 가 매우 큰 프로그램 지원

     - 32 bit address 사용시 : 2^32 (4G)의 주소 공간 (2^10 : K , 2^20 :M, 2^30 :G)
       - page size 가 4K시 1M개의 page table entry 필요 
       - 각 page entry 가 4B시 프로세스당 4M의 page table 필요 -> 프로그램마다 페이지 테이블을 위해 4M의 공간이 필요하다. -> 공간남비가 심함 그래서 2단계 페이지 테이블을 사용한다. 
       - 그러나, 대부분의 프로그램은 4G 의 주소 공간 중 지극히 일부분만 사용하므로 page table 공간이 심하게 낭비됨

   - page table 자체를 page 로 구성

   - 사용되지 않는 주소 공간에 대한 outer page table 의 엔트리 값은 NULL(대응하는 inner page  table 이 없음) -> 프로그램을 구성하는 공간 중 사용안되는 부분이 많다. 1단계 page table로 만들 때에는 중간에 사용되지 않는 공간의 엔트리 또한 포함해서 만들어야 한다. (k번째면 k번째에 가서 주소 변환을 해야 하기 때문, 중간에 엔트리를 없애 버리면 인덱스로 접근이 불가능) 2단계 페이지 테이블을 쓰게 되면 그게 해소됨. 바깥쪽 페이지 테이블은 전체 엔트리를 포함해서 만들지만 사용 안되는 엔트리에 대해서 안쪽 테이블을 만들지 않으면(NULL)로 둬버리면 되기 때문

     ![twolevelpagetable2](/assets/images/operating-system/twolevelpagetable2.png)

     p1을 통해 p2 중 어떤 테이블을 가르키는 지 알게된다.

     p2 를 통해 물리적인 페이지 프레임을 알게 된다. p2 의 크기는 page 크기와 똑같다. 

     거기서 d 번째 떨어진 위치에서 원하는 주소를 얻게 된다. 

5. Multilevel Paging and Performance

   - Address space 가 더 커지면 다단계 페이지 테이블 필요
   - 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address 의 physcial address 변환에 더 많은 메모리 접근 필요
   - TLB 를 통해 메모리 접근 시간을 줄일 수 있음
   - 4단계 페이지 테이블을 사용하는 경우
     - 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns 이고 
     - TLB hit ratio 가 98% 인 경우 effective memory access time = 0.98  * 120 + 0.02 *520 = 128 nanoseconds. 결과적으로 주소변환을 위해 28ns 만 소요(100은 메모리 접근 시간)

6. Memory Protection

   ![validinvalid](/assets/images/operating-system/validinvalid.png)

   v - 해당 페이지가 물리주소의 해당 프레임에 실제로 올라 가 있다는 뜻

   i -  해당 페이지가 이 프로그램에서 사용되지 않거나, 당장 필요하지 않아서 backing store 에 내려가 있을 수 도 있어 물리주소에 올라가 있지 않다는 뜻

   - Page table 의 각 entry 마다 아래의 bit를 둔다
   - Protection bit
     - page에 대한 접근 권한 (read/write/ready-only) - 어떤 연산에 대해 접근 권한이 있는지
     - read-only : code 의 부분은 바뀌지 않아야 하기 때문
     - read, write : data 영역이나 stack 영역은 데이터를 중간에 쓰고 업데이트 하기 때문
   - Valid-invalid bit
     - "valid" 는 해당 주소의 frame 에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함(접근 허용)
     - "invalid"는 해당 주소의 frame 에 유효한 내용이 없음을 뜻함(접근 불허)
       - 프로세스가 그 주소 부분을 사용하지 않는 경우
       - 해당 페이지가 메모리에 올라와 있지 않고 swap area 에 있는 경우

7. Inverted(역방향) Page Table - page table 의 공간 오버헤드를 줄여 보자고 나온 것

   - page table 이 매우 큰 이유
     - 모든 process 벼로 그 logical address 에 대응하는 모든 page 에 대해 page table entry 가 존재
     - 대응하는 page 가 메모리에 있든 아니든 간에 page table 에는 entry 로 존재

   ![inverted](/assets/images/operating-system/inverted.png)

   원래는 프로세스마다 페이지 테이블이 존재, inverted 는 시스템안에 페이지 테이블이 딱 하나 존재한다. page table 의 엔트리가 프로세스의 페이지 개수만큼 존재하는 게 아니고 물리적 메모리의 페이지 프레임 개수만큼 존재. 첫번째 엔트리는 물리적 메모리의 첫번째 프레임을 나타냄. 피지컬 어드레스를 보고 로지컬 어드레스로 바꿀 수 있는 테이블

   페이지의 공간은 줄일 수 있지만 페이지 테이블을 전체 탐색해야 하기 때문에 시간적인 오버헤드가 발생한다.

   - Inverted page table 
     - Page frame 하나당 page table 에 하나의 entry 를 둔 것(system-wide)
     - 각 page table entry 는 각각의 물리적 메모리의 page frame 이 담고 있는 내용 표시(process-id, process 의 logical address)
     - 단점
       - 테이블 전체를 탐색 해야함 (페이지번호로 엔트리를 바로 접근할 수 있었던 장점이 사라짐)
     - 조치
       - associative register 사용 (expensive)

8. Shared Page - 다른 프로세스와 공유할 수 있는 페이지

   ![sharedpage](/assets/images/operating-system/sharedpage.png)

   - Shared code
     - Re-entrant Code(=Pure code)
     - **read-only**로 하여 프로세스 간에 하나의 code 만 메모리에 올림 (ex. text editors, compilers, window systems)
     - Shared code는 모든 프로세스의 **logical address space 에서 동일한 위치**에 있어야 함
   - Private code and data
     - 각 프로세스들은 독자적으로 메모리에 올림
     - Private data 는 logical address space 의 아무 곳에 와도 무방





*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

