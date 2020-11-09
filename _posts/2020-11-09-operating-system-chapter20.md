---
layout: post
title: "[운영체제] File Systems"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "File and File System, Directory and Logical Disk, open( ), File Protection, File System의 Mounting, Access Methods"
featured: false
---

1. File and File System

   - File
     - "A named collection of related information" - (메모리는 주소를 통해서 접근, 파일은 이름을 통해서 접근)
     - 일반적으로 비휘발성의 보조기억장치에 저장(하드 디스크)
     - 운영체제는 다양한 저장 장치를 file 이라는 동일한 논리적 단위로 볼 수 있게 해 줌
     -  Operation
       - create, read, write, reposition (lseek), delete, open(파일의 메타데이터를 메모리에 올려 놓는 작업), close 등
   - File attribute (혹은 파일의 metadata)
     - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
       - 파일 이름, 유형, 저장된 위치, 파일 사이즈
       - 접근 권한 (읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등
   -  File system
     - 운영체제에서 파일을 관리하는 부분
     - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
     - 파일의 저장 방법 결정
     - 파일 보호 등

2. Directory and Logical Disk

   - Directory
     - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
     - 그 디렉토리 밑에 있는 파일이 어떤 것이 있는 지
     - 그 디렉토리에 속한 파일 이름 및 파일 attribute 들
     - operation
       - search for a file, create a file, delete a file
       - list a directory, rename a file, traverse the file system
   - Partition (=Logical Disk)
     - 하나의 (물리적) 디스크 안에 여러 파티션을 두는 게 일반적
     - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
     - (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음 - 디스크 용도 파일 시스템, 스왑 에리어

3. open()

   파일의 메타데이터를 메모리에 올려 두는 것

   ![open](/assets/images/operating-system/open.png)

   - open("/a/b/c")
     - 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
     - 이를 위하여 directory path를 search
       - 루트 디렉토리 "/" 를 open 하고 그 안에서 파일 "a"의 위치 획득
       - 파일 "a"를 open 한 후 read 하여 그 안에서 파일 "b"의 위치 획득
       - 파일 "b" 를 open 한 후 read 하여 그 안에서 파일 "c"의 위치 획득
       - 파일 "c" 를 open 한다.
     - Directory path 의 search 에 너무 많은 시간 소요
       - Open 을 read/write 와 별도로 두는 이유임
       - 한번 open 한 파일은 read/write 시 directory search 불필요
     -  Open file table
       - 현재 open 된 파일들의 메타데이터 보관소 (in memory)
       - 디스크의 메타데이터보다 몇 가지 정보가 추가
         - Open 한 프로세스의 수
         - File offset : 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)
     - File descriptor (file handle, file control block)
       - Open file table 에 대한 위치 정보 (프로세스 별) 

4. File Protection

   ![fileprotection](/assets/images/operating-system/fileprotection.png)

   메모리는 프로세스마다 별도로 가지고 있기 때문에 그 프로세스만 사용하여 read/write 권한에 대한 프로텍션만 해주면 되었다. 하지만 파일은 여러 프로그램이 같이 사용할 수 있기 때문에 누구에게 접근 권한이 있느냐, 접근 연산은 어떠한게 가능한지 두 가지를 같이 가지고 있어야 한다.  Access control Matrix 는 오버헤드가 크다. password 는 접근 권한에 따라 패스워드가 또 다르기 때문에 여러가지의 password 가 생기고 password 를 기억하기 어려운 문제가 있다. 그래서 일반적으로는 grouping 을 사용한다.

5.  File System 의 Mounting

   ![mounting](/assets/images/operating-system/mounting.png)

   disk1 에서 disk 2 나 disk3 의 파일에 접근하기 위해 Mounting 연산 사용 

   루트 파일 시스템의 특정 디렉토리 이름에다가 또 다른 파티션의 파일시스템을 마운팅 해주면 그 파일 시스템에 접근하면 다른 파티션의 루트 파일시스템에 접근이 가능하다.

6. Access Methods
   - 시스템이 제공하는 파일 정보의 접근 방식
     - 순차 접근 (sequential access)
       - 카세트 테이프를 사용하는 방식처럼 접근
       - 읽거나 쓰면 offset은 자동적으로 증가
     - 직접 접근 (direct access, random access)
       - LP 레코드 판과 같이 접근하도록 함
       - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음



*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

