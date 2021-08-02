---
layout: post
title: "[Spring Boot] REST API Project [2] - Swagger"
date: 2021-07-04
excerpt: "REST API Project - Swagger 설정"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---

# 1. Swagger 란?
* Open Api Specification(OAS)를 위한 프레임워크이다.
* API들이 가지고 있는 스펙(spec)을 명세, 관리할 수 있는 프로젝트/문서
* API 사용 방법을 사용자에게 알려주는 문서
* Springboot에서 Swagger를 사용하면, 컨트롤러에 명시된 어노테이션을 해석하여 API문서를 자동으로 만들어준다.
* 참고로 Swagger는 Java에 종속된 라이브러리가 아니다.
* URL에 /swagger-ui.html으로 접근하면 swagger가 만들어주는 페이지에 접근할 수 있다. 
  &nbsp;&nbsp;<br>EX) [http://localhost:9440/swagger-ui.html](http://localhost:9440/swagger-ui.html)
* [https://swagger.io/](https://swagger.io/)

# 2. Swagger의 기능

1)<b> API Design (API 설계)</b>: Swagger-editor를 통해 api를 문서화하고 빠르게 명세 가능 <br>
2)<b> API Development </b>: Swagger-codepen을 통해 작성된 문서를 통해 SDK를 생성하여 빌드 프로세스를 간소화할 수 있도록 도와준다. <br>
3)<b> API Documentation </b>: Swagger-UI를 통해 작성된 API를 시각화시켜준다. <br>
4)<b> API Testing </b>: Swagger-Inspector를 통해 API를 시각화하고 빠른 테스팅을 진행할 수 있다. <br>
5)<b> Standardize </b>: Swagger-hub를 통해 개인, 팀원들이 API 정보를 공유하는 Hub <br>

# 3. Swagger 설정

## 1. Swagger dependency 설정
(위치: build.gradle )

{% highlight gradle %}
   //swagger
   implementation 'io.springfox:springfox-swagger2:2.9.2'
   implementation 'io.springfox:springfox-swagger-ui:2.9.2'
   implementation 'io.swagger:swagger-annotations:1.5.21'
   implementation 'io.swagger:swagger-models:1.5.21'
{% endhighlight %}

## 2. SwaggerConfig.java 생성
(위치: src/main/java/com/yoon/api/config/Swagger.Config.java)

{% highlight java %}
package com.yoon.api.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2	//Swagger2 버전을 활성화 하겠다는 어노테이션
@Profile("dev")  // 백도어방지
public class SwaggerConfig {

	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo()).select()
				.apis(RequestHandlerSelectors.basePackage("com.yoon.api")).paths(PathSelectors.any()).build();
	}

	private ApiInfo apiInfo() {
		return new ApiInfoBuilder().title("REST API Study").description("This documents describes about REST API")
				.version("1.0.0").build();
	}
} 
{% endhighlight %}



 
#### @ Around
* 어느 시점에 적용할 것인지를 정의하는 것.
* @ Around 어드바이스는 앞서 설명한 어드바이스의 기능을 모두 포괄한다.
* 대상 메써드를 감싸는 느낌으로 실행 전후 시점에 원하는 작업을 할 수 있다. 대상 메써드의 실행 제어 및 리턴 값 가공도 가능하다.
* 메서드의 실행 전/후에 공통로직을 적용하고 싶을 때 사용하고 @ Before는 메서드 실행 전, @ After는 메서드 실행 후에 공통 로직을 적용하고 싶을 때 사용한다.

 
# 6. Swagger UI 확인

1) Project 아래에 API_Log 폴더 생성된 후 하위로 날짜별 log 파일 생성된것 확인.
<figure>
	<img src="/assets/img/log_directory.png">
</figure>

2) .log 파일 내용 확인
<figure>
	<img src="/assets/img/log_confirm.png">
</figure>

