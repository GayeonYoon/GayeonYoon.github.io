---
layout: post
title: "[Spring Boot] REST API Project [3]"
date: 2021-07-04
excerpt: "REST API Project - JPA를 이용한 CRUD 구현"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse, Lombok]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---

log 와 Swqgger 설정이 완료됐으니 본격적으로 REST API 를 들어가보자 !


# 1. Application 
{% highlight java %}  
package com.yoon.api;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.ApplicationPidFileWriter;

@SpringBootApplication
public class RestapiApplication {

	public static void main(String[] args) {
		// SpringApplication.run(RestapiApplication.class, args);   // 기존
    
		SpringApplication application = new SpringApplication(RestapiApplication.class);
		application.addListeners(new ApplicationPidFileWriter());
		application.run();
	} 
} 
{% endhighlight %}

# 2. Config

{% highlight java %}  
package com.yoon.api.config;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.querydsl.jpa.impl.JPAQueryFactory;

@Configuration
public class QuerydslConfig {

   @PersistenceContext
   private EntityManager entityManager;

   @Bean
   public JPAQueryFactory jpaQueryFactory() {
      return new JPAQueryFactory(entityManager);
   }
}
{% endhighlight %}

# 3. Controller

{% highlight java %}  
package com.yoon.api.controller;

import javax.validation.Valid;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.yoon.api.dto.CommonResponseDto;
import com.yoon.api.entity.ApiHistoryEntity;
import com.yoon.api.service.ApiHistoryService;

import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
//import lombok.RequiredArgsConstructor;

@RestController
@Api(tags = { "1.Api_History" }) //해당클래스가 Swagger 리소스라는 것을 명시함. swagger 세팅. 주소뒤에붙음 ex) https://localhost:9443/swagger-ui.html#/1.Api_History
@RequestMapping("/api/v1/history")
//@RequiredArgsConstructor  
public class ApiHistoryController {

   private final Logger logger = LoggerFactory.getLogger(this.getClass());

   @Autowired
   private ApiHistoryService apiHistoryService;
   
 //ApiHistoryService 에서 @RequiredArgsConstructor 를 사용할 경우
// Bean이 올라갈때 apiHistoryService를 @Autowired를 쓰지않고 static으로 선언해도 써도돼.
// final을 꼭 사용할것!! private final ApiHistoryService apiHistoryService;
    
   // Create => POST Method
   @ApiOperation(value = "History 생성", notes = "History 내역을 기록한다.")
   @PostMapping("/create")
   public ResponseEntity<CommonResponseDto> createHistory(@Valid @RequestBody ApiHistoryEntity apiHistoryParam) {

      logger.info(apiHistoryParam.toString());
      apiHistoryService.createHistory(apiHistoryParam);

      return new ResponseEntity<>(new CommonResponseDto("S00", "Create 성공했습니다."), HttpStatus.OK);
   }

   // Read => GET Method
   @ApiOperation(value = "History 조회", notes = "History 내역을 조회한다.")
   @GetMapping("/read/{historyKeyParam}")
   public ResponseEntity<CommonResponseDto> readHistory(@PathVariable String historyKeyParam) {
      logger.info(historyKeyParam);

      apiHistoryService.readHistory(historyKeyParam);
      return new ResponseEntity<>(new CommonResponseDto("S00", "Read 성공했습니다."), HttpStatus.OK);
   }

   // Update => PUT Method
   @ApiOperation(value = "History 수정", notes = "History 내역을 수정한다.")
   @PutMapping("/update") // id값이필요
   public ResponseEntity<CommonResponseDto> updateHistory(@Valid @RequestBody ApiHistoryEntity apiHistoryParam) {
      logger.info(apiHistoryParam.toString());
      apiHistoryService.updateHistory(apiHistoryParam);

      return new ResponseEntity<>(new CommonResponseDto("S00", "Update 성공했습니다."), HttpStatus.OK);
   }

   // Delete => DDELETE Method
   @ApiOperation(value = "History 삭제", notes = "History 내역을 삭제한다.")
   @DeleteMapping("/delete/{historyKeyParam}") // db에 동일한 객체가 필요
   public ResponseEntity<CommonResponseDto> deleteHistory(@PathVariable String historyKeyParam) {
      logger.info(historyKeyParam);
      apiHistoryService.deleteHistory(historyKeyParam);

      return new ResponseEntity<>(new CommonResponseDto("S00", "Delete 성공했습니다."), HttpStatus.OK);
   }

}
{% endhighlight %}

# 4. Dto
{% highlight java %}  
 package com.yoon.api.dto;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class CommonResponseDto {
  
	String resultCode;
	String resultMsg;
 
} 
{% endhighlight %}


# 5. Entity
{% highlight java %}  
package com.yoon.api.entity;

import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotBlank;

import com.sun.istack.NotNull;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
 
@Entity
@AllArgsConstructor
@NoArgsConstructor
@ToString
@Data
@Table(name = "api_history")
public class ApiHistoryEntity {

	@Id   // PK 설정
	@Column(name = "seq")
	@Min(1)
	@GeneratedValue(strategy = GenerationType.IDENTITY)   //DataBase에 키 생성방법을 위임함.
	private Long seq;

	@Column(name = "history_key")
	@NotNull
	@NotBlank
	private String historyKey;

	@Column(name = "req_date")
	private LocalDateTime reqDate;

	@Column(name = "data")
	private String data;

	@Column(name = "status")
	private String status;

	@Column(name = "expire_date")
	private LocalDateTime expireDate;

}
 
{% endhighlight %}

# 6. Repository
{% highlight java %}  
package com.yoon.api.repository;

import java.util.Optional;

import org.apache.ibatis.annotations.Param;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.querydsl.QuerydslPredicateExecutor;
import org.springframework.stereotype.Repository;

import com.yoon.api.entity.ApiHistoryEntity;

@Repository
public interface ApiHistoryRepository
		extends JpaRepository<ApiHistoryEntity, Long>, QuerydslPredicateExecutor<ApiHistoryEntity> {

	Optional<ApiHistoryEntity> findByHistoryKey(@Param("historyKey") String historyKey);

}

{% endhighlight %}

# 7. Repository Support
{% highlight java %}  
package com.yoon.api.repository.support;
 
import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
import org.springframework.stereotype.Repository;

import com.querydsl.jpa.impl.JPAQueryFactory;
import com.yoon.api.entity.ApiHistoryEntity;

import java.time.LocalDateTime; 
import javax.transaction.Transactional;

import static com.yoon.api.entity.QApiHistoryEntity.apiHistoryEntity;   //Querydsl 을 사용하기위한  Q Class!! 


@Repository
public class ApiHistoryRepositorySupport extends QuerydslRepositorySupport {
//Querydsl을 이용하여 실제 조회할 쿼리를 작성하는 클래스
	
	private final JPAQueryFactory jpaQueryFactory;  //QuerydslConfig 에서 @Bean으로 등록.

	public ApiHistoryRepositorySupport(JPAQueryFactory jpaQueryFactory) { 
		super(ApiHistoryEntity.class);
		this.jpaQueryFactory = jpaQueryFactory;
	}
 
	@Transactional
	public void updateByHistoryKey(String historyKey) {
		 jpaQueryFactory.update(apiHistoryEntity)
		 .where(apiHistoryEntity.historyKey.eq(historyKey))
		 .set(apiHistoryEntity.expireDate, LocalDateTime.now()).set(apiHistoryEntity.status, "Update").execute();
	}

	@Transactional
	public void deleteByHistoryKey(String historyKey) {  
		// JPADeleteClause deleteClause 
		 jpaQueryFactory.delete(apiHistoryEntity).where(apiHistoryEntity.historyKey.eq(historyKey)).execute();
	}

}  
{% endhighlight %}

# 8. Service
{% highlight java %}  
package com.yoon.api.service;

import com.yoon.api.entity.ApiHistoryEntity;

public interface ApiHistoryService {

	public boolean createHistory(ApiHistoryEntity apiHistoryParam);
	public ApiHistoryEntity readHistory(String historyKeyParam);
	public boolean updateHistory(ApiHistoryEntity apiHistoryParam);
	public boolean deleteHistory(String historyKeyParam);
	
}

{% endhighlight %}
  
# 9. Service Impl
{% highlight java %}  
package com.yoon.api.service.impl;

import java.util.Optional;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.yoon.api.entity.ApiHistoryEntity;
import com.yoon.api.repository.ApiHistoryRepository;
import com.yoon.api.repository.support.ApiHistoryRepositorySupport;
import com.yoon.api.service.ApiHistoryService;

@Service
public class ApiHistoryServiceImpl implements ApiHistoryService {

	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	@Autowired
	private ApiHistoryRepository apiHistoryRepo;

	@Autowired
	private ApiHistoryRepositorySupport apiHistoryRepoSupport;  
    
	@Override
	public boolean createHistory(ApiHistoryEntity apiHistoryParam) {
		apiHistoryRepo.save(apiHistoryParam);
		
		return true;
	}

	@Override
	public ApiHistoryEntity readHistory(String historyKeyParam) {
		//findByHistoryKey() return => Optional<ApiHistoryEntity>
		// orElse => NPE. Exception을 처리해줘. 발생하지않음. 필요할땐 get() 사용해.
		ApiHistoryEntity result = apiHistoryRepo.findByHistoryKey(historyKeyParam).orElse(new ApiHistoryEntity());
		logger.info(result.toString()); 
		return result;
	}

	@Override
	public boolean updateHistory(ApiHistoryEntity apiHistoryParam) {
		apiHistoryRepoSupport.updateByHistoryKey(apiHistoryParam.getHistoryKey());
		return true;
	}

	@Override
	public boolean deleteHistory(String historyKeyParam) {
		apiHistoryRepoSupport.deleteByHistoryKey(historyKeyParam);
		return true;
	}

}
{% endhighlight %}
 
