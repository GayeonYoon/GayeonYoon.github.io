---
layout: post
title: "[Spring Boot] Gmail SMTP 사용하기(메일보내기) "
date: 2021-08-07
excerpt: " Gmail SMTP 사용하기(메일보내기)"
tags: [Eclipse, SpringBoot, SMTP, Gmail, Java, Gradle, yml]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg
---

# 1. MailHandler (Config)

{% highlight java %}
package com.example.demo.config;

import java.io.File;
import java.io.IOException;

import javax.mail.MessagingException;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;

import lombok.ToString;

@ToString
public class MailHandler {
	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	private JavaMailSender sender;
	private MimeMessage message;
	private MimeMessageHelper messageHelper;

	// 생성자
	public MailHandler(JavaMailSender jSender) throws MessagingException {
		this.sender = jSender;
		message = jSender.createMimeMessage();
		messageHelper = new MimeMessageHelper(message, true, "UTF-8");
	}

	// 보내는 사람 이메일
	public void setFrom(String fromAddress) throws MessagingException {
		messageHelper.setFrom(fromAddress);
	}

	// 받는 사람 이메일
	public void setTo(String email) throws MessagingException {
		messageHelper.setTo(email);
	}

	// 제목
	public void setSubject(String subject) throws MessagingException {
		messageHelper.setSubject(subject);
	}

	// 메일 내용
	public void setText(String text, boolean useHtml) throws MessagingException {
		messageHelper.setText(text, useHtml);
	}

	// 첨부 파일
	public void setAttach(String displayFileName, String pathToAttachment) throws MessagingException, IOException {
		File file = new ClassPathResource(pathToAttachment).getFile();
		FileSystemResource fsr = new FileSystemResource(file);

		messageHelper.addAttachment(displayFileName, fsr);
	}

	// 참조자 설정
	public void setCc(String[] cc) throws MessagingException {
		messageHelper.setCc(cc);
	}

	public void setCc(InternetAddress[] cc) throws MessagingException {
		messageHelper.setCc(cc);
	}

	// 숨은참조자 설정
	public void setBcc(String[] bcc) throws MessagingException {
		messageHelper.setBcc(bcc);
	}

	public void setBcc(InternetAddress[] bcc) throws MessagingException {
		messageHelper.setBcc(bcc);
	}

	// 이미지 삽입
	public void setInline(String contentId, String pathToInline) throws MessagingException, IOException {
		File file = new ClassPathResource(pathToInline).getFile();
		FileSystemResource fsr = new FileSystemResource(file);

		messageHelper.addInline(contentId, fsr);
	}

	// 발송
	public void send() {
		try {
			sender.send(message);
		} catch (Exception e) {
			logger.error(e.toString());
		}
	}
}
{% endhighlight%}

# 2. Controller

{% highlight java %}
package com.example.demo.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.domain.MailDto;
import com.example.demo.service.MailService;

import io.swagger.annotations.Api;

@Api
@RestController
@RequestMapping("/mail")
public class MailController {

	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	@Autowired
	private MailService mailService;

	@GetMapping("/mail")
	public String dispMail() {
		return "get Mail String";
	}

	@PostMapping("/mail")
	public void execMail(@RequestBody MailDto mailDto) {
		logger.info(mailDto.toString());
		mailService.mailSend(mailDto);
	}
}

{% endhighlight%}

# 3. Service

{% highlight java %}
package com.example.demo.service;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.stereotype.Service;

import com.example.demo.config.MailHandler;
import com.example.demo.domain.MailDto;

@Service
public class MailService {

	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	@Autowired
	private JavaMailSender mailSender;

	@Value("${data.from.address}")
	private String FROM_ADDRESS;

	public void mailSend(MailDto mailDto) {
		System.out.println("====================");
		System.out.println("FROM_ADDRESS : " + FROM_ADDRESS);
		System.out.println("====================");
		
		try {
			MailHandler mailHandler = new MailHandler(mailSender);
			mailHandler.setTo(mailDto.getAddress());
			mailHandler.setFrom(FROM_ADDRESS);
			mailHandler.setSubject(mailDto.getTitle());
  // <img src='cid:sample-img'>
			String htmlContent = "<p>" + mailDto.getMessage() + "<p>";

			mailHandler.setText(htmlContent, true);

			System.out.println("mailHandler : "+mailHandler);
			mailHandler.send();

		} catch (Exception e) {
			logger.error(e.toString());
		}

	}
} 
{% endhighlight%}
  
# 4. DTO
    
{% highlight java %}
package com.example.demo.domain;

import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Data
@NoArgsConstructor
@ToString
public class MailDto {
	private String address;
	private String title;
	private String message;

}

{% endhighlight%}

# 5. application.yml
    
{% highlight yml %}
  
# 환경 설정 및 공통 설정
# Swagger 확인
# localhost:9443/swagger-ui.html#/
spring:
  profiles:
    active:
    - dev
  kafka:
    consumer:
      bootstrap-servers:
      - 127.0.0.1:9092
      group-id: foo
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      auto-offset-reset: earliest
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
      bootstrap-servers:
      - 127.0.0.1:9092
  mail:
    host: smtp.gmail.com
    port: 587       # TLS의 포트번호는 587이며 SSL의 포트번호는 465이다. 
    username: yoonpiri@gmail.com
    password: password  //인증받은 password
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true
  pid:
    file: apiboot.pid
  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        format_sql: true
### create, update, drop , none , create-drop, validate
  mvc:
    view:
      prefix: /WEB-INF/views/
      suffix: .jsp
  
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

mybatis:
  configuration:
    map-underscore-to-camel-case: true
    default-fetch-size: 100
    default-statement-timeout: 30
  type-aliases-package: com.example.demo.mapper
  mapper-locations:
  - /mapper/*.xml


graphql:
  spqr:
    gui:
      enabled: true

# 외부 변수
data:
  from:
    address: yoonpiri@gmail.com
  url:
    target: https://127.0.0.1:9441/api/v1/res/test1
  http:
    port: 9444


---

#test 용 port 및 keystore 위치
spring:
  profiles: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?autoReconnect=true&useUnicode=true?characterEncoding=utf8&serverTimezone=UTC
    username: root
    password: 1234
  task:
    fixedDelay: 5000
     
server:
  port: 9443
#  ssl:
#    key-store: D:/.keystore
#    key-store-password: ssbrtest
#    key-store-type: JKS
  shutdown: graceful
    

{% endhighlight%}
  
  
  
# 1. Help -> Install New Software 클릭  
<figure>
	<img src="/assets/img/quick-search1.png">
</figure>

# 2. Add... 클릭 -> Name, Location 입력 -> Add 클릭
* Name: QuickSearch
* Location: http://dist.springsource.com/release/TOOLS/update/e4.3/

<figure>
	<img src="/assets/img/quick-search2.png">
</figure> 

# 3. QuickSearch 체크
Work with 에 입력한 Name과 Location 확인 -> Core/Eclipse Integration Commons 클릭 -> Eclipse QuickSearh 만 체크 -> Next
<figure>
	<img src="/assets/img/quick-search3.png">
</figure> 

# 4. Restart
Restart 후 Ctrl + Shift + L 로 확인하면 현재 WorkSpace 내에 있는 모든 해당되는것을 찾아준다.. Good👍 (없음못살아)
 
