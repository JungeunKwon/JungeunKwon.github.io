---
layout: post
title: "메모리 관리 전략"
author: Jungeun
categories: [operating-system]
tags: [OS, operating-system, 공룡책, 메모리관리, 페이징]
description: "메모리에 로드된 프로세스를 효율적으로 관리하는 방법을 다루는 것에 관한 설명"
featured: false
---

### Swapping

------

메모리는 크기가 크지 않기 때문에 프로세스를 임시로 디스크에 보냈다가 다시 메모리에 로드해야 하는 상황이 생긴다. 이때 디스크로 내보내는 것을 swap out, 메모리로 들여보내는 것을 swap in 이라고 하며, 우선 순위에 따라 어떤 프로세스를 swap in/out 할지 결정한다. swap 하는데 걸리는 시간의 대부분은 디스크 전송 시간이다.

### CMA(Contiguous Memory Allocation)

------

연속 메모리 할당, 보통 메모리는 두 개의 영역으로 나눠 관리되는데, low memory에는 커널을, high memory 에는 사용자 프로세스를 담는다. 이때 contiguous memory allocation 시스템에서는 각 프로세스들이 연속적인 메모리 공간을 차지하게 된다. 프로세스가 자신의 범위를 넘지 못하도록 하는 것은 base register와 limit register의 역할이다. 

#### Memory Allocation

프로세스를 메모리에 로드할 때는 먼저 메모리 상에 프로세스를 넣을 수 있는 공간을 찾는다. 메모리를 분할하는 각 단위는 block 이고, 이 중 사용 가능한 block을 hole 라고 한다. 이때 할당하는데 여러 방법이 있다. 

- First fit : 첫 번째 hole 을 할당
- Best fit : hole 중에서 가장 작은 곳을 할당
- Worst fit :  가장 큰 곳을 할당

하지만 Best fit도 그닥 효율이 좋지 않아 이런 식으로는 쓸 수 없다.

### Fragmentation

------

Fragmentation 은 메모리 공간을 사용하지 못하게 되는 것을 말한다. 여러 프로세스에 메모리를 할당하는 과정을 거치면 메모리의 모습은 대략 아래 그림과 비슷하다. 

|      | empty |      |      | empty |
| ---- | ----- | ---- | ---- | ----- |
| 30k  | 60k   | 20k  | 40k  | 60k   |

각 block의 크기를 순서대로 30k, 60k, 20k, 40k, 60k 라고 하면 hole 은 60k 두 곳 뿐이다. 이때 70k의 프로세스가 들어와야 한다면, 실제 메모리 공간은 120k 가 비어있지만 어디에도 70k가 들어갈 수는 없다. 이것을 **external fragmentation**이라고 한다. **internal fragmentation**은 실제 프로세스 공간보다 큰 메모리를 할당하게 되는 경우를 말한다. 일반적으로 메모리가 시스템 효율을 위해 고정 크기의 정수 배로 할당되기 때문에 생기는 현상이다. 이런 문제는 할당된 block 을 한쪽으로 몰아 큰 block을 생성하는 compaction으로 해결할 수 있다. 

> **fragmentation** : 기억 장치의 빈 공간 또는 자료가 여러 개의 조각으로 나뉘는 현상
>
> internal fragmentation(내부 단편화) : 메모리를 할당할 때 프로세스가 필요한 양 보다 더 큰 메모리가 할당 되어서 프로세스에서 사용하는 메모리 공간이 낭비 되는 현상
>
> external fragmentation(외부 단편화) : 중간중간에 생긴 사용하지 않는 메모리가 존재해서 총 메모리 공간은 충분하지만 실제로 할당할 수 없는 상황 

|      |      |      | empty |
| ---- | ---- | ---- | ----- |
| 30k  | 20k  | 40k  | 120k  |

이렇게 하면 70K 프로세스도 들어갈 수 있다. 하지만 프로세스 할당은 정말 자주 일어나는 일이기 때문에 compaction 처럼 오버헤드가 큰 작업을 매번 할 수는 없다. 

### Paging

------

메인 메모리를 일정한 사이즈의 할당 공간인 frame 으로 쪼갠 뒤 프로세스도 그 크기에 맞게 나누어 할당하는 기법을 말한다. frame 사이즈에 맞춰 프로세스를 나눈 단위를 page라고 하는데, 이 페이지들은 페이지 테이블에 순서에 따라 저장된다.

> frame : physical memory(보조 기억 장치)의 각 block들
> page : logical memory(RAM)의 각 block들 

하나의 프로세스는 하나의 페이지 테이블을 갖고, 테이블은 인덱스와 내용으로 구성되어 있는데 인덱스는 페이지의 순서를, 내용은 메인 메모리에 들어가는 위치를 가지고 있다.

**특징**

- 연속적이지 않은 공간도 활용할 수 있기 때문에 외부 단편화(External Fragmentation)을 해결할 수 있다.
- 코드를 쉽게 공유할 수 있다.
- 페이지 단위를 작게하면 내부 단편화(Internal Fragmentation) 역시 해결할 수 있지만 그만큼 page mapping 과정이 증가한다. 

> paging 의 또 다른 장점은 코드를 쉽게 공유할 수 있다는 것이다. 만약 코드가 reentrant code(또는 pure code)라면 공유가 가능하다. reentrant code는  runtime동안 절대로 변하지 않는 코드이며, 따라서 여러 프로세스들이 동시에 같은 코드를 수행할 수 있다. 이런 식으로 공통 page를 공유하면 12개 로드해야 할 것을 6개만 로드해도 된다.

### Segmentation

------

Paging 기법은 가상메모리를 같은 크기의 단위로 분할했으나 Segmentation는 main, function, method, object 등의 논리적 단위인 세그먼트(segment)로 분할하고 메모리를 할당하며 주소 변환을 한다.  각 segment의 base와 limit은  segment table 에 저장된다. 

**세그먼트들의 크기가 서로 다르기 때문에 메모리를 페이징 기법처럼 미리 분할해 둘 수 없고,메모리에 적재될 때 빈 공간을 찾아 할당하는 사용자 관점의 가상메모리 관리 기법입니다.**

**특징**

- mapping을 위해 segment table 이 필요하다.
- 하나의 세그먼트 단위로 통제가 가능하므로 내부 단편화가 발생하지 않는다.
- 서로 다른 크기의 세그먼트들에 대해 필요시에 메모리에 올리고 필요없을 경우 내리는 작업을 반복하다보면 외부 단편화가 생기는 문제점이 있다.

출처: 

https://parksb.github.io/ [공룡책으로 정리하는 운영체제]

