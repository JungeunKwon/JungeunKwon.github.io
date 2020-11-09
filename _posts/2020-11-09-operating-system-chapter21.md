---
layout: post
title: "[운영체제] File Systems Implementation"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Allocation of File Data in Disk, Contiguous Allocation, Linked Allocation, Indexed Allocation, UNIX 파일시스템의 구조, FAT File System, Free-Space Management, Directory Implementation, VFS and NFS, Page Cache and Buffer Cache, 프로그램의 실행"
featured: false
---

1. Allocation of File Data in Disk

   - Contiguous Allocation
   - Linked Allocation
   - Indexed Allocation

2. Contiguous Allocation

   ![contiguousallocation](/assets/images/operating-system/contiguousallocation.png)

   directory 파일은 그 디렉토리에 있는 파일들의 메타데이터를 내용으로 한다. 그래서 지금 디렉토리에 파일이 5개가 있고 파일의 이름과 위치정보를 가지고 있다.

   연속할당으로 저장한다 하면 count 파일을 예를 들었을 때 위치가 0번이고 길이가 2이면 0번 1번에 할당하는 방식

   - 단점
     - external fragmentation - 각각의 파일들의 조각이 균일하지 않기 때문에 중간 중간에 내용이 들어 있지 않은 블럭들의 크기 또한 균일하지 않다. 만약 블럭이 3개 필요한 프로세스라면 17 18번에 할당될 수 없다.
     - File grow 가 어려움 - 파일이 수정되어서 크기가 더 커지게 될 수도 있다. tr 같은 경우 길이가 3늘어날려는데 공간은 2 밖에 없어서 커질 수 없다.
       - file 생성 시 얼마나 큰 hole 을 배당할 것인가? 
       - grow 가능 vs 낭비 (internal fragmentation)
   - 장점
     - Fast I/O
       - 한번의 seek/rotation 으로 많은 바이트 transfer
       - Realtime file 용으로, 또는 이미 run 중이던 process의 swapping 용
     - Direct access(=random access) 가능

3. Linked Allocation

   ![linkedallocation](/assets/images/operating-system/linkedallocation.png)

   연속적으로 배치하지 않고 빈 위치면 들어갈 수 있도록 하는 것. jeep 파일의 시작 위치는 9번이고 9번의 끝에가면 2번째 블록의 위치 정보가 있다. 

   - 장점
     - External fragmentation 발생 안 함
   - 단점
     - No random access - 4번째 블록을 볼려면 1, 2, 3 블록을 거쳐야 함
     - Reliability 문제 - 만약 블록이 10개인데 중간에 5번 sector 가 고장나 데이터가 유실되면 그 다음 위치는 모조리 다 유실되어 버림
       - 한 sector 가 고장나 pointer 가 유실되면 많은 부분을 잃음
     - Pointer 를 위한 공간이 block 의 일부가 되어 공간 효율성을 떨어뜨림 - 포인터를 위해 4bytes를 사용하게 되면 1섹터에 다 저장될 내용이 포인터 때문에 2섹터를 사용해 버림
       - 512 bytes/sector, 4 bytes/pointer
   - 변형
     - File-allocation table(FAT)파일 시스템
       - 포인터를 별도의 위치에 보관하여 reliability 와 공간 효율성 문제 해결

4. Indexed Allocation
   ![indexedallocation](/assets/images/operating-system/indexedallocation.png)

   직접 접근이 가능하게 하기 위해서 디렉토리에 파일의 위치정보를 바로 저장하는 게 아니라 인덱스 블럭을 가리키게 함, 인덱스 블록은 파일의 내용을 가르키는게 아니라 이 파일이 어디어디에 저장되어 있다는 위치 정보를 열거해 둠. 

   - 장점
     -  External fragmentation이 발생하지 않음 - 비어 있는 홀이면 바로 사용 가능
     - Direct access 가능
   - 단점
     - Small file 의 경우 공간 낭비 (실제로 많은 파일들이 small) - 블럭이 하나로 충분한데 두개가 필요
     - Too Large file 의 경우 하나의 block 으로 index를 저장하기에 부족
       - 해결 방안
         1. linked scheme -> 인덱스 블럭의 끝에 또 다른 인덱스 블럭의 위치를 가르키게 함. 인덱스를 여러개 만드는 것
         2. multil-level index 

5. FAT File System

   ![FAT](/assets/images/operating-system/FAT.png)

   파일의 메타데이터 중 일부 ( 위치정보 ) 만 저장해 두고 있다. 나머지 메타 정보와 첫번째 블럭 위치 디렉토리가 가지고 있다. 217번 블럭의 다음 테이블은 FAT 이라는 테이블에 담겨 있다. Linked Allocation 의 단점을 모두 커버.

6. Free-Space Management

   - Bit map or bit vector

     ![bitmap](/assets/images/operating-system/bitmap.png)

     - Bit map 은 부가적인 공간을 필요로 함
     - 연속적인 n 개의 free block 을 찾는데 효과적
     - 블럭의 개수 만큼 bit 가 구성 , 각각의 블럭 번호에 0이면 비어있는 값 1이면 할당된 블럭 번호

   - Linked list

     - 모든 free block 들을 링크로 연결 (free list)
     - 연속적인 가용공간을 찾는 것은 쉽지 않다
     - 공간의 낭비가 없다.

   - Grouping (indexed allocation과 비슷)

     - linked list 방법의 변형
     - 첫번째 free block 이 n 개의 pointer 를 가짐
       - n-1 pointer 는 free data block 을 가리킴
       - 마지막 pointer 가 가리키는 block 은 또 다시 n pointer 를 가짐

   - Counting

     - 프로그램들이 종종 여러 개의 연속적인 block 을 할당하고 반납한다는 성질에 착안
     - ( first free block , # of contiguous free blocks )을 유지
     - 빈 블럭의 위치와, 그 첫 빈 블럭부터 몇개가 비어있다 는 정보를 쌍으로 가짐

7. Directory Implementation

   - Linear list
     - <file name, file 의 metadata> 의 list
     - 구현이 간단
     - 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search 필요 (time - consuming)
   - Hash Table
     - linear list + hashing
     - Hash table 은 file name 을 이 파일의 linear 의 위치로 바꾸어줌
     - search time 을 없앰
     - Collision 발생 가능
   - File 의 metadata의 보관 위치
     - 디렉토리 내에 직접 보관
     - 디렉토리에는 포인터를 두고 다른 곳에 보관
       - inode, FAT 등
   - Long file name 의 지원
     - <file name, file의  metadata > 의 list 에서 각 entry 는 일반적으로 고정 크기
     - file name 이 고정 크기의 entry 길이보다 길어지는 경우 entry 의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
     - 이름의 나머지 부분은 동일한 directory file 의 일부에 존재

8. VFS and NFS

   - Virtual File System (VFS)
     - 서로 다른 다양한 file system 에 대해 동일한 시스템 콜 인터페이스 (API) 를 통해 접근할 수 있게 해주는 OS의 layer
     - 사용자가 파일 시스템에 접근할 때 다양한 파일 시스템 각각의 시스템 콜을 사용하지 않고 동일한 시스템 콜로 다양한 파일 시스템에 접근하게 해줌
   - Network File System (NFS)
     - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
     - NFS 는 분산 환경에서의 대표적인 파일 공유 방법임
     - 원격 시스템에 있는 파일에 접근해야할 때

9. Page Cache and Buffer Cache

   ![pagecacheandbuffercache](/assets/images/operating-system/pagecacheandbuffercache.png)

   - Page Cache - 운영체제에게 주어지는 정보가 지극히 제한적
     - Virtual memory 의 paging system 에서 사용하는 page frame 을 caching 의 관점에서 설명하는 용어
     - Memory-Mapped I/O를 쓰는 경우 file 의 I/O에서도 page cache 사용
   - Memory-Mapped I/O
     - File의 일부를 virtual memory에 mapping 시킴 - 그래서 read 나 write 를 하는 것이 아니라 메모리에 쓰는 것 처럼 보이지만 실제로는 파일에 쓰고 있는 동작을 할 수 있다.
     - 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 함
   - Buffer Cache - 파일을 접근할 때는 시스템콜을 해야하기 때문에 운영체제가 다 알고있음
     - 파일시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache 사용
     - File 사용의 locality 활용
       - 한번 읽어온 block 에 대한 후속 요청시 buffer cache 에서 즉시 전달
     - 모든 프로세스가 공용으로 사용
     - Replacement algorithm 필요 (LRU, LFU 등)
   - Unified Buffer Cache - 버퍼캐쉬도 페이지 단위로 관리를 한다는 것.  운영체제에서 이런 페이지 프레임 관리하는 루틴에 버퍼 캐쉬도 같이 관리한다는 것(합쳐서 관리) buffer cache 와 page cache 의 공간을 따로 나누어 두지 않고 필요에 따라서 page cache 에서 공간을 할당해서 사용
     - 최근의 OS 에서는 기존의 buffer cache 가 page cache 에 통합됨

   ![pagecacheandbuffercache2](/assets/images/operating-system/pagecacheandbuffercache2.png)

   1. 사용자 프로세스가 read 또는 write 를 요청하면 운영체제는 버퍼 캐쉬를 확인하고 없으면 파일시스템에서 버퍼 캐쉬에 올리고 사용자에게 제공
   2. memory-mapped I/O -> 프로그램이 운영체제에게 나는 memory-mapped I/O 를 쓰겠다고 요청 -> 자신의 주소 공간 중 일부를 파일에 매핑 버퍼 캐쉬에서 읽어오는 과정은 똑같음-> 읽어온 내용을 page cache 에 카피 -> 이 후 사용자 프로그램이 메모리에 읽고 쓰듯이 하면 파일에 read / write 됨( **운영체제의 간섭 없이 내 메모리에 데이터를 쓰는 방식을 통해 파일 입출력** - MM의 장점) MM만 해두고 메모리에 파일 영역을 안읽어 왔다면 page fault 가 발생

   -> ready write 시스템콜을 사용하든 MM을 사용하든 파일 입출력을 할 때 buffer cache 를 통과 해야한다. 버퍼 캐쉬의 내용을 자신의 페이지 캐쉬에 한번 복제를 해야하는 오버헤드가 있다.

   1. 사용자 프로세스가 read 또는 write 를 요청하면 운영체제에게 CPU 제어권이 넘어가서 buffer cache 에서 사용자 프로그램의 주소 영역에 카피 해준다. buffer cache 에 없다면 file system 에서 읽어와서 카피 해준다.
   2. memory - mapped I/O 에서는 이전 처럼 buffer cache의 내용을 자신의 page cache 에 한번 더 복사하는 것이 아니라 사용자의 논리적인 메모리 주소에 page cache가 맵핑이 된다. 그럼 page cache 에 직접 읽고 쓰기가 가능해진다.

10. 프로그램의 실행

    ![program](/assets/images/operating-system/program.png)

    1. 프로그램이 파일시스템의 실행파일로 저장되어 있다.
    2. 이 파일 시스템에서 실행 파일이 실행이 되면 프로세스가 된다.
    3. 프로세스가 되면 그 프로세스만의 독자적인 주소 공간 virtual memory 가 생성되고 code, data, stack 으로 구성된다. 
    4. 주소 변환을 해주는 하드웨어에 의해 프로그램에서 당장 필요한 부분은 물리적인 메모리에 올라가게 된다.
    5. 물리적인 메모리의 공간이 한정되어 있기 때문에 쫓겨나는 메모리들은 swap area 로 넘어간다. (사실 code 부분은 read-only 이기 때문에 쫓겨날 때 swap area 로 갈 필요가 없다. 이미 file system 에 있기 때문)

    ![program2](/assets/images/operating-system/program2.png)

    6. 프로그램이 실행이 되다가 데이터 파일을 쓰고 싶다. ready write I/O형태가 아닌 memory-mapped 형태로 쓰고 싶다.

    7. 운영체제에게 해당 데이터파일의 일부를 내 주소공간 일부에 매핑해달라는 시스템콜을 한다.
    8. 프로그램이 실행되면서 데이터파일의 메모리(까만색 블럭)에 접근 했는 데 메모리에 데이터 파일이 올라와 있지 않으면 page fault 가 발생한다.
    9. 그럼 CPU가 OS로 넘어가서 page fault 가 난 부분을 물리적인 메모리에 올려준다.
    10. 프로그램의 데이터파일의 메모리영역이 물리적인 데이터 파일의 메모리 영역에 매핑이 되고 나면 OS의 도움을 받지 않고 (자신의 주소 공간중 하나이기 때문에) 읽거나 쓰거나 할 수 있다.
    11. 나중에 데이터파일의 메모리 영역이 쫓겨나게 된다면 swap area 로 가는 게 아니라 파일에 수정된 내용을 써주고 쫓아내야 한다. 
    12. 프로세스 A 도 동일한 파일에 대한 memory-mapped 요청을 하면 B로 인해 올려진 데이터파일의 물리적인 메모리 주소에 매핑이 되어 B 와 공유하게 된다. 
    13. A가 만약에 read / write I/O로 시스템 콜을 하게 되면 운영체제는 자신의 버퍼 캐쉬에 데이터파일의 내용을 읽어 온다. 근데 Unified Buffer Cache 라면 주소 공간이 page cache 이며 buffer cache 를 겸하고 있기에 page cache 에 A 가 요청한 데이터 파일이 있다면 그 내용을 카피해서 A 에게 전달해준다.
    14. 그래서 memory - mapped I/O 는 일단 메모리에 한번 올라오면 시스템 콜을 하지 않고 직접 접근할 수 있기 떄문에 더 빠른 장점이 있고, 캐쉬에 올라온 내용을 자신의 주소공간에 한번 더 카피 하는 오버헤드가 없이 그대로 바뀐 내용이 전달되는 장점이 있다. 
    15. 주의할 점은 다른 프로세스와 해당 영역을 공유하게 된다면 일관성 문제에 대해 조심해야한다.

    

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

