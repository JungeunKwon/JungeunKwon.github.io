---
layout: post
title: "[운영체제] Memory Management 3"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "Segmentation, Segmentation Architecture, Segmentataion Hardware,Segmentation, Segmentation Hardware, Segmentation Architecture, Example of Segmetation, Segmentation Architecture(Cont.), Sharing of Segments, Segmentation with Paging, Address Translation Architecture"
featured: false
---

1. Segmentation 

   - 프로그램은 의미 단위인 여러 개의 segment 로 구성
   - 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
   - 일반적으로는 code, data, stack 부분이 하나씩의 세그먼트로 정의 됨
   - Segment 는 다음과 같은 logical unit 들임
     - main(), function, global variables, stack, symbol table, arrays

2. Segmentation Architecture

   - Logical address 는 다음의 두 가지로 구성 <segment-number, offset> - 세그번트 번호와, 세그먼트 안에서 얼마나 떨어져있는 지 오프셋
   - Segment table - 세그먼트 별로 서로 다른 물리적인 메모리 위치에 올라갈 수 있기 때문에 세그먼트 별로 테이블이 필요
     - each table entry has :
       - base - starting physical address of the segment
       - limit - length of the segment
   - Segment-table base register (STBR)
     - 물리적 메모리에서의 segment table 의 위치
   - Segment-table length register (STLR)
     - 프로그램이 사용하는 segment 의 수 segment number s is legal if s<STLR.

   ![segmentation](/assets/images/operating-system/segmentation.png)

   CPU 가 논리주소를 주게 되면 세그먼트 번호 (s) 와 offset(d) 으로 나뉜다. 세그먼트 테이블의 시작위치에서 s 만큼 떨어진 엔트리에 가면 그 세그먼트가 물리적 메모리의 어떤 번지에 올라가 있는 지를 가지고 있다. page table 과는 다르게 물리적 메모리의 시작 위치와 limit 라고 세그먼트의 길이가 얼만지 같이 가지고 있다. paging 에서는 page 의 크기가 다 동일 했지만 세그먼트 기법은 의미단위로 자르기 때문에 세그먼트의 길이가 균일하지 않을 수 있기 때문이다.

   CPU 가 준 논리주소에서 세그먼트 번호가 STLR 에서 세그먼트 갯수 보다 큰 값이면 trap 

   세그먼트를 통해 주소변환을 하는데 세그먼트의 길이 (limit) 보다 세그먼트이 offset (d)이 더 크다면 trap

3. Segmentation Architecture (Cont.)

   - Protection
     - 각 세그먼트 별로 protection bit 가 있음
     - Each entry : 
       - Valid bit = 0 -> illegal segment
       - Read/Write/Execution 권한 bit
   - Sharing
     - shared segment
     - same segment number (공유 세그먼트의 번호는 같아야 한다.)
     - segment 는 의미 단위이기 때문에 공유(sharing)와 보안(prtection)에 있어 paging 보다 훨씬 효과적이다. -> paging 에서는 어떤 프레임엔 코드와 데이터가 같이 들어갈 수도 있기 때문
   - Allocation
     - first fit / best fit
     - external fragmentation 발생
     - segment 의 길이가 동일하지 않으므로 가변 분할 방식에서와 동일한 문제점들이 발생 (paging 은 빈 공간이 생기지 않음)

4. Segmentation with Paging (=Paged Segmentation)

   ![segmentationwithpaging](/assets/images/operating-system/segmentationwithpaging.png)

   세그먼트 하나가 여러개의 페이지로 구성되어 있다. 메모리에 올라갈 때는 페이지 단위로 올라가진다. => 세그먼트에서의 문제점인 external fragmentation 문제가 발생하지 않게 된다. 또한 보안이나 공유는 세그먼트 테이블 레벨에서 처리를 한다. 

   먼저 세그먼트에 대한 주소 변환이 이루어 진다. 각 프로그램이 가지고 있는 논리 주소는 세그먼트 번호와 offset 이 주어진다. 세그먼트 테이블을 찾으면 페이지 테이블의 시작위치가 나온다. 그럼 페이지 테이블로 가서 물리적인 주소로 변환을 한다. (각각의 세그먼트 당 페이지 테이블이 존재한다.) 세그먼트 테이블의 길이는 페이지 테이블의 엔트리 갯수다. 

   논리주소의 offset을 다시 잘라서 앞부분은 페이지 번호 뒷부분은 페이지 안에서 얼마나 떨어졌는지 offset으로 사용한다. 

이번 챕터는 물리적 메모리에 대한 메모리 관리에 대한 내용이다. 프로세스가 논리적인 주소를 가지고 있고 CPU가 논리적인 주소를 주면 그거를 물리적인 메모리주소로 변환하여 물리적인 메모리를 참조하는 것이 이번 챕터 내용. 이 챕터에서는 주소 변환을 위한 운영체제의 역할은 없다. 다 **하드웨어**가 해주어야하는 역할

*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

