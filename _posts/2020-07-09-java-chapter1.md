---
layout: post
title: "자바 JVM"
author: Jungeun
categories: [java]
tags: [java, JVM, 자바메모리]
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

