---
layout: post
title: "[SpringBoot] REST와 REST API에 대하여 알아보자."
date: 2021-07-04
excerpt: "REST API란 무엇인가?"
tags: [REST, REST API]
comments: false
spring: true

---

REST API란 REST를 기반으로 만들어진 API를 의미한다. 

우선 REST부터 알아보자. 



# REST란 ? #

REST(Representational State Transfer)의 약자로 자원을 이름(자원의 표현)으로 구분하여 
해당 자원의 상태를 주고받는 모든 것을 의미한다.

1. HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고,
2. HTTP Method(POST, GET, PUT, DELETE)를 통해
3. 해당 자원(URI)에 대한 CRUD  Operation 적용하는 것을 의미한다.

 참고) CRUD란 ?

 CRUD는 대부분의 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제) 를 말한다.

* Create : 데이터 생성(POST)
* Read : 데이터 조회(GET)
* Update : 데이터 수정(PUT)
* Delete : 데이터 삭제(DELETE)

## REST 장 ·단점 ##

* 장점
  * HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라를 구출할 필요가 없다.
  * HTTP 프로토콜의 표준을 최대한 활용하여 여러 추가적인 장점을 함께 가져갈 수 있게 해준다.
  * HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
  * Hypermedia API의 기본을 충실히 지키면서 범용성을 보장한다.
  * REST API 메시지가 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
  * 여러가지 서비스 디자인에서 생길 수 있는 문제를 최소화한다.
  * 서버와 클라이언트의 역할을 명확하게 분리한다.

* 단점
  * 표준이 존재하지 않는다.
  * 사용할 수 있는 메소드가 4가지이다. (HTTP Method 형태가 제한적임)
  * 브라우저를 통해 테스트할 일이 많은 서비스라면 쉽게 고칠 수 있는 URL보다 Header 값이 왠지 더 어렵게 느껴진다.
  * 구형 브라우저가 아직 제대로 지원해주지 못하는 부분이 존재한다.
  * PUT, DELETE를 사용하지 못하는 점
  * pushState를 지원하지 않는 점 

## REST 구성 요소 ##

1. 자원(Resource): URI
  * 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
  * 자원을 구별하는 ID는 ‘/groups/:group_id’와 같은 HTTP URI 다.
  * Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.

2. 행위(Verb): HTTP Method
  * HTTP 프로토콜의 Method를 사용한다.
  * HTTP 프로토콜은 GET, POST, PUT, DELETE 와 같은 메서드를 제공한다.

3. 표현(Representation of Resource)
  * Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답(Representation)을 보낸다.
  * REST에서 하나의 자원은 JSON, XML, TEXT, RSS 등 여러 형태의 Representation으로 나타내어 질 수 있다.
  * JSON 혹은 XML를 통해 데이터를 주고 받는 것이 일반적이다.

  
  

------



# REST API란 ? #

API(Application Programming Interface)란 ?

데이터와 기능의 집합을 제공하여 컴퓨터 프로그램간 상호작용을 촉진하며, 서로 정보를 교환가능 하도록 하는 것

## REST API의 정의 ##
REST API(REpresentational State Transfer)는 웹상에서 사용되는 여러 리소스를 HTTP URI로 표현하고, 해당 리소스에 대한 행위를 HTTP Method로 정의하는 방식을 말합니다.

즉, REST 기반으로 서비스 API를 구현한 것
최근 OpenAPI(누구나 사용할 수 있도록 공개된 API: 구글 맵, 공공 데이터 등), 마이크로 서비스(하나의 큰 애플리케이션을 여러 개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능하도록 만든 아키텍처) 등을 제공하는 업체 대부분은 REST API를 제공한다. 

## REST API의 특징 ##

1. REST 기반으로 시스템을 분산해 확장성과 재사용성을 높여 유지보수 및 운용을 편리하게 할 수 있다.

2. HTTP 표준을 기반으로 구현하므로 HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현할 수 있다.
 
3. 즉, REST API를 제작하면 델파이 클라이언트 뿐 아니라, 자바, C#, 웹 등을 이용해 클라이언트를 제작할 수 있다. 


## REST API 설계 규칙 ##

1. URI는 명사를 사용한다.
2. 슬래시로 계층 관계를 표현한다.
3. URI의 마지막에는 슬래시를 붙이지 않는다.
4. URI는 소문자로만 구성한다.
5. 가독성이 떨어지는 경우 하이픈을 사용한다.
 
 
 REST 와 REST API에 대하여 알아보았으니 이제 SpringBoot를 이용한 Rest API Project를 시작해보자 !  


------
>참고 
 https://www.redhat.com/ko/topics/api/what-is-a-rest-api
 https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html
 https://mangkyu.tistory.com/46
 https://brainbackdoor.tistory.com/53
 https://khj93.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-REST-API%EB%9E%80-REST-RESTful%EC%9D%B4%EB%9E%80
 https://velog.io/@taeha7b/api-restapi-restfulapi


