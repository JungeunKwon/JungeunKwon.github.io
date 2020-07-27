---
layout: post
title: "JVM"
author: Jungeun
categories: [java]
tags: [java, JVM, 자바메모리, method, heap, stack]
description: "자바 JVM과 JVM의 메모리 구조"
featured: false
---

### JVM

JVM : Java Virtual Machine, 자바 가상 머신의 약자로 자바의 바이트 코드를 운영체제 언어로 변환해줌으로써 자바가 운영체제에 구애받지 않고 사용을 가능하게 해준다.

**자바 프로그램 실행 과정** 

1. 프로그램이 실행되면 JVM은 OS부터 메모리를 할당 받는다. JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.class)로 변환시킨다.
3. 클래스 로더를 통해 class파일들을 JVM으로 로딩한다.
4. 해석된 바이트 코드는 Runtime Data Areas에 배치되어 실질적인 수행이 이루어지게 된다. 

이러한 실행 과정 속에서 JVM 은 필요에 따라 스레드 동기와 GC 같은 관리작업을 수행한다.

### JVM 메모리 구조

![1507](/assets/images/java/jvm.png)

1. JVM 메모리 구조

   - Java Source : 사용자가 작성한 Java 코드
   - Java Compiler : Java 코드를 Byte Code 로 변환시켜주는 기능
   - Class Loader : Class 파일을 메모리(Runtime Data Area)에 적재하는 기능
   - Execution Engine : Byte Code를 실행 가능하게 해석해주는 기능
   - Runtime Data Area : 프로그램을 수행하기 위해 OS에서 할당 받은 메모리 공간

2. Runtime Data Area

   - Class Area(Method Area)

     - 클래스에 대한 정보를 저장
     - 멤버 변수와 메서드, 클래스 타입 등의 정보를 저장
     - Static 변수
     - final class 변수의 경우 (상수로 치환되어) 상수 풀에 값 복사

   - Stack Area 

     - 메서드 호출 시 각각의 스택 프레임 생성
     - 호출된 메서드의 매개변수, 지역변수, 리턴 값 및 연산 시 일어나는 값들을 임시로 저장
     - 메서드 수행이 끝나면 프레임별로 삭제
     - 기본 자료형

   - Heap Area

     - new 연산자로 생성된 객체와 배열을 저장하는 공간
     - 클래스 영역에 로드된 클래스만 생성 가능
     - 참조 자료형
     - Garbage Collector를 통해 메모리 반환

   - Native method stack area

     - 자바 외의 다른 언어에서 제공되는 메서드들이 저장되는 공간

   - PC Register

     - Thread가 생성 될 때마다 생성되는 공간
     - 현재 실행되는 부분의 명령과 주소를 저장

     

     

     

     

     

     

     