---
layout: post
title: "[SpringBoot] REST와 REST API에 대하여 알아보자."
date: 2021-07-04
excerpt: "REST API란 무엇인가?"
tags: [SpringBoot, REST API, JAVA, Eclipse]
comments: true
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

REST 기반으로 서비스 API를 구현한 것
최근 OpenAPI(누구나 사용할 수 있도록 공개된 API: 구글 맵, 공공 데이터 등), 마이크로 서비스(하나의 큰 애플리케이션을 여러 개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능하도록 만든 아키텍처) 등을 제공하는 업체 대부분은 REST API를 제공한다. 

## REST API의 특징 ##

1. REST 기반으로 시스템을 분산해 확장성과 재사용성을 높여 유지보수 및 운용을 편리하게 할 수 있다.

2. REST는 HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현할 수 있다.
 
3. 즉, REST API를 제작하면 델파이 클라이언트 뿐 아니라, 자바, C#, 웹 등을 이용해 클라이언트를 제작할 수 있다. 


## REST API 설계 규칙 ##

1. 슬래시 구분자( / )는 계층 관계를 나타내는데 사용한다.
* Ex) http://restapi.example.com/houses/apartments 

2. URI 마지막 문자로 슬래시( / )를 포함하지 않는다.
* URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며 URI가 다르다는 것은 리소스가 다르다는 것이고, 역으로 리소스가 다르면 URI도 달라져야 한다. 
* REST API는 분명한 URI를 만들어 통신을 해야 하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는 슬래시(/)를 사용하지 않는다.
* Ex) http://restapi.example.com/houses/apartments/ (X)

3. 하이픈( - )은 URI 가독성을 높이는데 사용한다.
* 불가피하게 긴 URI경로를 사용하게 된다면 하이픈을 사용해 가독성을 높인다.

4. 언더바( _ )는 URI에 사용하지 않는다.
* 밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 하므로 가독성을 위해 밑줄은 사용하지 않는다.

5. URI 경로에는 소문자가 적합하다.
* URI 경로에 대문자 사용은 피하도록 한다.
* RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문

6. 확장자는 URI에 포함하지 않는다.
* API에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
* Acept header를 사용한다.
* EX) http://restapi.example.com/members/soccer/345/photo.jpg (X)
* Ex) GET / members/soccer/345/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg (O)

7.리소스 간에는 연관 관계가 있는 경우

* /리소스명/리소스 ID/관계가 있는 다른 리소스명
* Ex) GET : /users/{userid}/devices (일반적으로 소유 ‘has’의 관계를 표현할 때)







Portland in shoreditch Vice, labore typewriter pariatur hoodie fap sartorial Austin. Pinterest literally occupy Schlitz forage. Odio ad blue bottle vinyl, 90's narwhal commodo bitters pour-over nostrud. Ugh est hashtag in, fingerstache adipisicing laboris esse Pinterest shabby chic Portland. Shoreditch bicycle rights anim, flexitarian laboris put a bird on it vinyl cupidatat narwhal. Hashtag artisan skateboard, flannel Bushwick nesciunt salvia aute fixie do plaid post-ironic dolor McSweeney's. Cliche pour-over chambray nulla four loko skateboard sapiente hashtag.

Vero laborum commodo occupy. Semiotics voluptate mumblecore pug. Cosby sweater ullamco quinoa ennui assumenda, sapiente occupy delectus lo-fi. Ea fashion axe Marfa cillum aliquip. Retro Bushwick keytar cliche. Before they sold out sustainable gastropub Marfa readymade, ethical Williamsburg skateboard brunch qui consectetur gentrify semiotics. Mustache cillum irony, fingerstache magna pour-over keffiyeh tousled selfies.

## Cupidatat 90's lo-fi authentic try-hard

In pug Portland incididunt mlkshk put a bird on it vinyl quinoa. Terry Richardson shabby chic +1, scenester Tonx excepteur tempor fugiat voluptate fingerstache aliquip nisi next level. Farm-to-table hashtag Truffaut, Odd Future ex meggings gentrify single-origin coffee try-hard 90's.

* Sartorial hoodie
* Labore viral forage
* Tote bag selvage
* DIY exercitation et id ugh tumblr church-key

Incididunt umami sriracha, ethical fugiat VHS ex assumenda yr irure direct trade. Marfa Truffaut bicycle rights, kitsch placeat Etsy kogi asymmetrical. Beard locavore flexitarian, kitsch photo booth hoodie plaid ethical readymade leggings yr.

Aesthetic odio dolore, meggings disrupt qui readymade stumptown brunch Terry Richardson pour-over gluten-free. Banksy american apparel in selfies, biodiesel flexitarian organic meh wolf quinoa gentrify banjo kogi. Readymade tofu ex, scenester dolor umami fingerstache occaecat fashion axe Carles jean shorts minim. Keffiyeh fashion axe nisi Godard mlkshk dolore. Lomo you probably haven't heard of them eu non, Odd Future Truffaut pug keytar meggings McSweeney's Pinterest cred. Etsy literally aute esse, eu bicycle rights qui meggings fanny pack. Gentrify leggings pug flannel duis.

## Forage occaecat cardigan qui

Fashion axe hella gastropub lo-fi kogi 90's aliquip +1 veniam delectus tousled. Cred sriracha locavore gastropub kale chips, iPhone mollit sartorial. Anim dolore 8-bit, pork belly dolor photo booth aute flannel small batch. Dolor disrupt ennui, tattooed whatever salvia Banksy sartorial roof party selfies raw denim sint meh pour-over. Ennui eu cardigan sint, gentrify iPhone cornhole.

> Whatever velit occaecat quis deserunt gastropub, leggings elit tousled roof party 3 wolf moon kogi pug blue bottle ea. Fashion axe shabby chic Austin quinoa pickled laborum bitters next level, disrupt deep v accusamus non fingerstache.

Tote bag asymmetrical elit sunt. Occaecat authentic Marfa, hella McSweeney's next level irure veniam master cleanse. Sed hoodie letterpress artisan wolf leggings, 3 wolf moon commodo ullamco. Anim occupy ea labore Terry Richardson. Tofu ex master cleanse in whatever pitchfork banh mi, occupy fugiat fanny pack Austin authentic. Magna fugiat 3 wolf moon, labore McSweeney's sustainable vero consectetur. Gluten-free disrupt enim, aesthetic fugiat jean shorts trust fund keffiyeh magna try-hard.

## Hoodie Duis

Actually salvia consectetur, hoodie duis lomo YOLO sunt sriracha. Aute pop-up brunch farm-to-table odio, salvia irure occaecat. Sriracha small batch literally skateboard. Echo Park nihil hoodie, aliquip forage artisan laboris. Trust fund reprehenderit nulla locavore. Stumptown raw denim kitsch, keffiyeh nulla twee dreamcatcher fanny pack ullamco 90's pop-up est culpa farm-to-table. Selfies 8-bit do pug odio.

### Thundercats Ho!

Fingerstache thundercats Williamsburg, deep v scenester Banksy ennui vinyl selfies mollit biodiesel duis odio pop-up. Banksy 3 wolf moon try-hard, sapiente enim stumptown deep v ad letterpress. Squid beard brunch, exercitation raw denim yr sint direct trade. Raw denim narwhal id, flannel DIY McSweeney's seitan. Letterpress artisan bespoke accusamus, meggings laboris consequat Truffaut qui in seitan. Sustainable cornhole Schlitz, twee Cosby sweater banh mi deep v forage letterpress flannel whatever keffiyeh. Sartorial cred irure, semiotics ethical sed blue bottle nihil letterpress.

Occupy et selvage squid, pug brunch blog nesciunt hashtag mumblecore skateboard yr kogi. Ugh small batch swag four loko. Fap post-ironic qui tote bag farm-to-table american apparel scenester keffiyeh vero, swag non pour-over gentrify authentic pitchfork. Schlitz scenester lo-fi voluptate, tote bag irony bicycle rights pariatur vero Vice freegan wayfarers exercitation nisi shoreditch. Chambray tofu vero sed. Street art swag literally leggings, Cosby sweater mixtape PBR lomo Banksy non in pitchfork ennui McSweeney's selfies. Odd Future Banksy non authentic.

Aliquip enim artisan dolor post-ironic. Pug tote bag Marfa, deserunt pour-over Portland wolf eu odio intelligentsia american apparel ugh ea. Sunt viral et, 3 wolf moon gastropub pug id. Id fashion axe est typewriter, mlkshk Portland art party aute brunch. Sint pork belly Cosby sweater, deep v mumblecore kitsch american apparel. Try-hard direct trade tumblr sint skateboard. Adipisicing bitters excepteur biodiesel, pickled gastropub aute veniam.
