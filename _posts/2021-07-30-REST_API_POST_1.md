---
layout: post
title: "[Spring Boot] REST API Project - Log4j, Swqgger [1]"
date: 2021-07-04
excerpt: "REST API Project - Project 생성, Log4j, Swagger 설정"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---

# 1. SpringBoot 프로젝트 생성

##1. Eclipse 실행 후 File > New > Other > Spring Boot > Spring Starter Project 클릭 후 <br> Project <b>Name</b> 입력, Type은 <b>Gradle</b>로 선택.
<figure>
	<img src="/assets/img/RestApi_post1.png">
</figure>
 
##2. Project 생성 완료 후 구조는 아래와같이 나타남.
<figure>
	<img src="/assets/img/RestApi_post2.png">
</figure>

*** 참고<br>
1) Spring Boot에서는 기본적으로 "spring-boot-starter-test"를 제공해줌. <br>
 * @SpringBootTest 를 통해 스프링부트 어플리케이션 테스트에 필요한 거의 모든 의존성을 제공한다.<br>
 * com/example/demo/RestapiApplication.java 와 RestapiApplicationTests.java 참고<br>
 * 프로젝트 생성 후 Run as 를 하면 바로 실행되는것을 볼 수 있음. <br>

2) Gradle 프로젝트로 만들었기때문에 build.gradle 등과 같은 파일들이 생성됨. <br>

# 2. build.gradle 에서 dependencies 등을 추가함.
  
{% highlight gradle %}
buildscript {
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


# 3. application.properties를 application.yml로 변경.<br>
(위치 : src/main/resources/application.properties) <br>
* DB는 MySQL 과 DBeaver 사용!

{% highlight yml %}
spring:
  profiles:
    active:
    - dev
  pid:
    file: apiboot.pid
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        format_sql: true
  mvc:
    view:
      prefix: /WEB-INF/views/
      suffix: .jsp

#log4j 설정 
logging:
  file:
    path: ./API_Log
  level:
    org:
      hibernate:
        SQL: debug
        type:
          descriptor:
            sql: trace
    root: info

---

spring:
  profiles: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?autoReconnect=true&useUnicode=true?characterEncoding=utf8&serverTimezone=UTC
    username: root
    password: 1234

server:
  port: 9440
{% endhighlight %}


# 4. logback-spring.xml 파일 추가<br>
(위치 : src/main/resources/logback-spring.xml)<br>
1) application.yml 에서 logging path 를 ./API_LOG 로 설정.<br>
2) logback-spring.xml 에서 fileNamePattern을 <b>${LOG_PATH}</b>/APIServer_log.%d{yyyy-MM-dd}-%i.log 로 설정.<br>

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 콘솔에 로그 출력 --> 
    <appender name="STDOUT"
        class="ch.qos.logback.core.ConsoleAppender">  
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{yyyyMMdd HH:mm:ss.SSS} [%thread] %-3level %logger{5} - %msg %n</pattern>
        </encoder>
    </appender>

    <!-- 로그 파일 생성 root/spring.log/~~ -->
    <appender name="dailyRollingFileAppender"
        class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy
            class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/APIServer_log.%d{yyyy-MM-dd}-%i.log
            </fileNamePattern>
            <maxHistory>30</maxHistory>
            <timeBasedFileNamingAndTriggeringPolicy
                class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-3level %logger{5} - %msg %n
            </pattern>
        </encoder>
    </appender>
 
    <!-- 프로필별 로깅 -->
    <root level="INFO">
        <springProfile name="dev">
            <appender-ref ref="STDOUT" />   <!-- 이게 콘솔출력 / 없으면 콜솔출력 X , log 파일에만 출력. -->
            <appender-ref ref="dailyRollingFileAppender" />
        </springProfile>
        <springProfile name="op">
            <appender-ref ref="dailyRollingFileAppender" />
        </springProfile>
    </root>
</configuration>
{% endhighlight %}
 
# 5. LogAspect.java 파일 생성
   (위치 : src/main/java/com/yoon/api/config.LogAspect.java ) <br><br>
1) LogAspect.java 에서 <b>공통기능을 정의하고 공통기능이 사용될 시점을 정의</b>한다. <br>
2) @ Aspect, @ Component로 AOP가 바라보는 관점을 정의하고 bean으로 등록한다.<br>
3) @ Around Advice에서 Pointcut은 within 으로, JoinPonts은 within(com.yoon.api..*) 로 설정한다.<br>
 
{% highlight java %}
package com.yoon.api.config;

import java.util.Map;
import java.util.stream.Collectors;

import javax.servlet.http.HttpServletRequest;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import com.google.common.base.Joiner;

@Aspect
@Component
public class LogAspect {

	private static final Logger logger = LoggerFactory.getLogger(LogAspect.class);

	@Around("within(com.yoon.api..*)")   // com.yoon.api 패키지 및 하위 패키지의 모든 결합점 (클래스 포함)
	public Object logging(ProceedingJoinPoint pjp) throws Throwable {

		String params = getRequestParams();
		String type = "";
		String name = pjp.getSignature().getDeclaringTypeName();

		// method가 불려오는 이름이 컨트롤러명 타입을 Controller로 찍고
		if (name.contains("Controller")) {
			type = "Controller";
		} else if (name.contains("Service")) {
			type = "ServiceImpl";
		} else if (name.contains("Mapper")) {   // mybatis에서만 작동.
			type = "Mapper";
		}

		long startAt = System.currentTimeMillis();
		logger.info("{} ====> REQUEST : {}({}) = {}", type, pjp.getSignature().getDeclaringTypeName(),
				pjp.getSignature().getName(), params);
		Object result = pjp.proceed();
		long endAt = System.currentTimeMillis();
		logger.info("{} ====> RESPONSE : {}({}) = {} ({}ms)", type, pjp.getSignature().getDeclaringTypeName(),
				pjp.getSignature().getName(), result, endAt - startAt);

		return result;

	}

	// get requset value
	private String getRequestParams() {

		String params = "";
		RequestAttributes requestAttribute = RequestContextHolder.getRequestAttributes();

		if (requestAttribute != null) {
			HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes())
					.getRequest();
			Map<String, String[]> paramMap = request.getParameterMap();
			if (!paramMap.isEmpty()) {
				params = " [" + paramMapToString(paramMap) + "]";
			}
		}
		return params;
	}

	private String paramMapToString(Map<String, String[]> paramMap) {
		return paramMap.entrySet().stream()
				.map(entry -> String.format("%s -> (%s)", entry.getKey(), Joiner.on(",").join(entry.getValue())))
				.collect(Collectors.joining(", "));
	}
    
}
{% endhighlight %}
 
#### @ Around
* 어느 시점에 적용할 것인지를 정의하는 것.
* @ Around 어드바이스는 앞서 설명한 어드바이스의 기능을 모두 포괄한다.
* 대상 메써드를 감싸는 느낌으로 실행 전후 시점에 원하는 작업을 할 수 있다. 대상 메써드의 실행 제어 및 리턴 값 가공도 가능하다.
* 메서드의 실행 전/후에 공통로직을 적용하고 싶을 때 사용하고 @ Before는 메서드 실행 전, @ After는 메서드 실행 후에 공통 로직을 적용하고 싶을 때 사용한다.

 
# 6. Log 파일 확인

1) Project 아래에 API_Log 폴더 생성된 후 하위로 날짜별 log 파일 생성된것 확인.
<figure>
	<img src="/assets/img/log_directory.png">
</figure>

2) .log 파일 내용 확인
<figure>
	<img src="/assets/img/log_confirm.png">
</figure>

