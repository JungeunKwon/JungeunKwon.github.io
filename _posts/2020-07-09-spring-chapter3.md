---
layout: post
title: "스프링 프레임워크"
author: Jungeun
categories: [spring]
tags: [spring, 스프링부트로배우는자바웹개발, 프레임워크, IoC, DI, 제어의역전, 의존성주입]
description: "스프링 프레임워크와 MVC에 대한 개념 설명"
featured: false
---

[TOC]

### 빈 +  컨테이너

스프링은 객체 관리를 해주는 빈 컨테이너 프레임워크다. 

처음에 자바 빈들을 대신해서 관리해 줄 컨테이너를 찾게 되었고 그게 EJB (Enterprise Java Bean)였다. 그러나 개발에 대한 요구 사항이 점점 복잡해지자 좀 더 경량화되고 간소화된 컨테이너를 선호하게 되었다. 그래서 사람들은 EJB가 필요한지에 대해서 고민하기 시작했고 그 시작은 **POJO(Plain Old Java Object)**이다. POJO는 간단히 말해서 일반 자바 클래스이다. EJB와 상충되는 방법으로 개발을 진행하기 시작하였고 일반 자바 빈이라는 말 대신에 POJO라는 말을 사용하기 시작했다. 그리고 로드 존슨의 'J2EE 설계와 개발'이라는 책을 시작으로 웹 애플리케이션 컨테이너와 상관없이 독립적으로 빈의 생명주기를 관리할 수 있는 스프링 프레임워크를 만들게 되었다.

### Ioc패턴

**Ioc(Inversion of Control : 제어의 역전)** 스프링 모듈간의 의존도를 낮추는 방법 중 하나, 프로그램의 생명주기에 대한 주도권이 WAS에 있다. 

자바 기반의 웹 프로그램 개발시 클라이언트 요청에 따라 파일 처리나 데이터베이스 처리를 하는 클래스들의 인스턴스 관리를 해야 하고, 일괄적으로 인스턴스 관리를 위해서 객체의 생성을 관리할 수 있는 도구의 필요성이 대두되었다.  같은 의미로 디자인 패턴의 원칙 중에는 의존 관계 역전 원칙이 있다. 

> 의존 관계 역전 원칙은 두 가지 내용을 담고 있다.
>
> 1. 하이레벨 모듈은 로우레벨 모듈에 의존해서는 안 되고, 모두 인터페이스에 의존해야 한다.
> 2. 추상화는 세부 사항에 의존해서는 안된다. 
>
> => 인터페이스를 활용함으로써 결합도를 낮추게 하자는 뜻.

그런데 인터페이스를 사용하더라도 객체 생성에 필요한 코드가 수반되므로 결합도를 완전히 분리해 낼 수 없다. 결국 프로그램이 온전히 동작하기 위해서는 인스턴스화할 수 있는 코드에 대한 의존성을 갖게 되었고 이를 대신 해결해 주는 것이 바로 **DI(Dependency Injection : 의존성 주입)**이다. 

> 의존성이란 클래스나 인터페이스들을 사용하기 위해서 해야 하는 행위들

스프링 컨테이너에 클래스를 등록하면 스프링이 클래스의 인스턴스를 관리해 준다. 스프링 컨테이너에 빈(Bean)을 등록하고 설정하는 방법은 XML 과 어노테이션 두 가지가 있다. 설정 파일에 스프링이 정의한 방식대로 클래스 정보를 입력하면 개발자가 new 연산자를 사용해서 클래스의 인스턴스를 생성하지 않아도 스프링이 관리해준다. 빈으로 등록하거나 제거할 때 콜백 메서드를 등록 함으로써 객체의 생성 여부와 소멸 여부를 파악할 수 있게 된다.

### 스프링 MVC

**스프링 MVC 는 프론트 컨트롤러 패턴에 Spring 의 의존성 주입을 이용해서 컴포넌트들의 생명주기를 관리할 수 있는 컨트롤러 중심의 웹  MVC 프레임워크이다.**

스프링 MVC는 DispatcherServlet, View Resolver, Interceptor, Handler, View 등으로 구성되어 있다.

![스프링 MVC 요청 처리 흐름도](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/b9dcef34-b94e-4a14-b288-b0425c037de0/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200709%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200709T080038Z&X-Amz-Expires=86400&X-Amz-Signature=a5a1913afc77ce24ce9b65e136a33c459143155cd23532009992d108f3997974&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

[사진 출처](https://www.notion.so/WEB-a174d9799610470b9db8ac8730dfc32b)

1. DispatcherServlet이 요청을 받음
2. 요청을 처리할수있는 Handler 이름을 HandlerMapping에게 물어봄
3. HandlerMapping은 요청 URL을 보고 Handler를 판단하며 실행 전 전처리, 후처리 할 인터셉터 목록을 결정.
4. 제어권을 Handler로 전달
5. Handler는 응답에 필요한 서비스를 호출
6. 응답에서 렌더링해야 하는 View Name을 판단해서 DispatcherServlet에 전송
7. DispatcherServlet은 논리적인 View Name을 ViewResolver에 전달
8. 응답에 필요한 View 생성
9. 해당 View에 Model과 컨트롤러를 전달해서 응답을 생성
10. 응답을 클라이언트에 반환

> FrontController 패턴에서는 Command 패턴과 RequestDispatcher을 이용해서 직접 분배하는것을 구현했지만, 스프링 MVC 프레임워크에선 DispatcherServlet을 web xml에 등록하고 요청받은 루트URL 맵핑만으로 해당 역할 대체가능

> 스프링 2.5 버전부터 어노테이션 기반 설정이 추가되어서 @Controller, @Service 와 같은 어노테이션을 이용해서 개발을 진행할 수 있다.

### 인터셉터

인터셉터는 가로챈다는 뜻으로 컨트롤러가 요청을 처리하기 전 혹은 처리 후에 대해 로직을 추가할 수 있다. 주로 특정 URL에 대해 공통 로직 적용이 필요한 경우에 많이 사용한다.

##### 인터셉터와 JSP Filter의 차이

- **공통점 :** 둘 다 Controller로 들어가는 요청을 가로채 특정 작업을 하기 위한 용도로 사용된다.
- **차이점** : 케어할 수 있는 영역(범위)가 다르다. Filter는 같은 웹 어플리케이션 내에서만 접근이 가능하며,

인터셉터의 경우 스프링에서 관리되기 때문에 스프링내의 모든 객체에 접근이 가능하다.

→ JSP Filter의 경우 주로 한글처리에 이용되고

→ Interceptor의 경우 "로그인 처리"에 이용이 된다.

**왜?**

만약 인터셉터를 이용하지 않고, 로그인 처리를 한다면, 게시물을 작성("/board/register"), 게시물 수정("/board/modify"),

게시물 삭제("/board/delete") 등 모든 요청마다 Controller에서 session을 통해 로그인 정보가 남아 있는지를 확인하는 코드

를 중복해서 입력해야 할 것이다.

하지만!, 인터셉터를 이용하면, A, B, C 작업(A,B,C 경로로 요청)을 할 경우에는 ~~Interceptor를 먼저 수행해 session에서

로그인 정보가 있는지 확인해 주는 역할을 한다면, 중복 코드가 확 줄어들 수 있을 것이다. 이러한 장점 때문에 사용!

##### 인터셉터 실행순서

preHandler → index Controller → postHandle → afterComplete