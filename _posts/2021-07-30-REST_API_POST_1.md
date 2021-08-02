---
layout: post
title: "[Spring Boot] REST API Project [1]"
date: 2021-07-04
excerpt: "REST API Project - 설정"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---

# 1. SpringBoot 프로젝트 생성

1. Eclipse 실행 후 File > New > Other > Spring Boot > Spring Starter Project 클릭 후 <br>
Project <b>Name</b> 입력, Type은 <b>Gradle</b>로 선택.
<figure>
	<img src="/assets/img/RestApi_post1.png">
</figure>
 
2. Project 생성 완료 후 구조는 아래와같이 나타남.
<figure>
	<img src="/assets/img/RestApi_post2.png">
</figure>

*** 참고<br>
1) Spring Boot에서는 기본적으로 "spring-boot-starter-test"를 제공해줌. <br>
 * @SpringBootTest 어노테이션을 통해 스프링부트 어플리케이션 테스트에 필요한 거의 모든 의존성을 제공한다.<br>
 * com/example/demo/RestapiApplication.java 와 RestapiApplicationTests.java 참고<br>
 * 프로젝트 생성 후 Run as 를 하면 바로 실행되는것을 볼 수 있음. <br>
2) Gradle 프로젝트로 만들었기때문에 build.gradle 등과 같은 파일들이 생성됨. <br>

# 2. build.gradle 에서 dependencies 등을 추가함.
  
{% highlight code %} buildscript {
   dependencies {
       classpath("gradle.plugin.com.ewerk.gradle.plugins:querydsl-plugin:1.0.10")
   }
}

plugins {
   id 'org.springframework.boot' version '2.4.5'
   id 'io.spring.dependency-management' version '1.0.11.RELEASE'
   id 'java'
}

apply plugin: "com.ewerk.gradle.plugins.querydsl"

bootJar{
   launchScript()
}


group = 'com.example'
version = 'v1.0.1'
sourceCompatibility = '1.8'

configurations {
   compileOnly {
      extendsFrom annotationProcessor
   }
}

repositories {
   mavenCentral()
}

dependencies {
   implementation 'org.springframework.boot:spring-boot-starter-web'
   implementation 'org.springframework.boot:spring-boot-starter-jdbc'
   implementation 'org.springframework.boot:spring-boot-starter-webflux'
   implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.4'
   compileOnly 'org.projectlombok:lombok'
   developmentOnly 'org.springframework.boot:spring-boot-devtools'
   runtimeOnly 'mysql:mysql-connector-java'
   annotationProcessor 'org.projectlombok:lombok'
   testImplementation 'org.springframework.boot:spring-boot-starter-test'
   testImplementation 'io.projectreactor:reactor-test'
   
   implementation 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16'
   
   //view
   implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
   implementation 'javax.servlet:jstl:1.2'
   
   //swagger
   implementation 'io.springfox:springfox-swagger2:2.9.2'
   implementation 'io.springfox:springfox-swagger-ui:2.9.2'
   implementation 'io.swagger:swagger-annotations:1.5.21'
   implementation 'io.swagger:swagger-models:1.5.21'
   
   //jpa
   implementation "org.springframework.boot:spring-boot-starter-data-jpa"
   implementation "com.h2database:h2"
   
   //cache
   implementation 'org.springframework.boot:spring-boot-starter-cache'

   //valid
   implementation 'org.springframework.boot:spring-boot-starter-validation'

   //kafka
   implementation 'org.springframework.kafka:spring-kafka'
   
   //graphql
   implementation 'com.graphql-java-kickstart:graphql-spring-boot-starter:6.0.1' 
    runtimeOnly 'com.graphql-java-kickstart:graphiql-spring-boot-starter:6.0.1'
    
    //mail
    implementation 'org.springframework.boot:spring-boot-starter-mail'
    
    //webSocket
    implementation 'org.springframework.boot:spring-boot-starter-websocket'
    
      //security
   //implementation 'org.springframework.boot:spring-boot-starter-security'
   //implementation 'org.springframework.security:spring-security-test'
    
       
    //queryDSL
    implementation 'com.querydsl:querydsl-jpa'
    implementation 'com.querydsl:querydsl-apt'
}

test {
   useJUnitPlatform()
}

def querydslDir = 'src/main/generated'

querydsl {
    library = "com.querydsl:querydsl-apt"
   jpa = true
   querydslSourcesDir = querydslDir
}

sourceSets {
   main {
       java {
          srcDirs = ['src/main/java', querydslDir]
          }
   }
}

compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}

configurations {
    querydsl.extendsFrom compileClasspath
}

{% endhighlight %}







 

