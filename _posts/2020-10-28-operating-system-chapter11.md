---
layout: post
title: "[운영체제] Process Management"
author: Jungeun
categories: [operating-system]
tags: [OS, 반효경 교수님 강의]
description: "프로세스 생성(Process Creation), 프로세스 종료(Process Termination), 프로세스와 관련한 시스템콜, 프로세스 간 협력, Message Passing, Interprocess communication"
featured: false
---

1. 프로세스 생성 (Process Creation)
   - 부모 프로세스(Parent process) 가 자식 프로세스(Childern process) 생성
   - 프로세스의 트리(계층 구조)형성
   - 프로세스는 자원을 필요로 함
     - 운영체제로부터 받는다
     - 부모와 공유한다
   - 자원의 공유 - 보통은 공유하지 않는 모델이 일반적이다.
     - 부모와 자식이 모든 자원을 공유하는 모델
     - 일부를 공유하는 모델
     - 전혀 공유하지 않는 모델
   - 수행(Execution)
     - 부모와 자식은 공존하며 수행되는 모델
     - 자식이 종료(terminate) 될 때까지 부모가 기다리는 (wait) 모델
   - 주소 공간 (Address space)
     - 자식은 부모의 공간을 복사함 - 운영체제의 자원들 PCB 등
     - 자식은 그 공간에 새로운 프로그램을 올림
   - 유닉스의 예
     - fork() 시스템 콜이 새로운 프로세스를 생성
       - 부모를 그대로 복사 (OS data except PID)
       - 주소 공간 할당
     - fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림
2. 프로세스 종료 (Process Termination)
   - 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (**exit**)
     - 자식이 부모에게 output data 를 보냄 (via **wait**)
     - 프로세스의 각종 자원들이 운영체제에게 반납됨
   - 부모 프로세스가 자식의 수행을 종료시킴 (**abort**) - 강제 종료
     - 자식이 할당 자원의 한계치를 넘어섬
     - 자식에게 할당된 태스크가 더 이상 필요하지 않음
     - 부모가 종료(exit) 하는 경우
       - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더이상 수행되도록 두지 않는다.
       - 단계적인 종료
3. 프로세스와 관련한 시스템 콜
   - fork () : create a child(copy)
   - exec () : overlay new image
   - wait () : sleep until child is done
   - exit () : frees all the resources, notify parent
4. 프로세스 간 협력
   - 독립적 프로세스 (Independent process)
     - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
   - 협력 프로세스 (Cooperating process)
     - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
   - 프로세스 간 협력 메커니즘 (IPC : Interprocess Communication)
     - 메세지를 전달하는 방법
       - message passing : 커널을 통해 메시지 전달
     - 주소 공간을 공유하는 방법
       - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는  shared memory 메커니즘이 있음







*출처: [이화여대 반효경 교수님 강의]( http://www.kocw.net/home/search/kemView.do?kemId=1046323)*

