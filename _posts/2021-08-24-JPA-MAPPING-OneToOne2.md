---
layout: post
title: "[Spring Boot] Jpa Mapping 연관관계 2 "
date: 2021-08-24
excerpt: "Jpa Mapping 연관관계(일대일 양방향)"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---






# 연관관계 (단방향과 양방향)

## 연관관계 매핑 
- 방향 : 단방향, 양방향
- 다중성 : 일대일, 일대다, 다대일, 다대다
- 연관관계 주인 : 객체 양방향 연관관계는 주인이 필요함! 

# 1. 일대일 단방향 

## 1. Entity  
### 1. Host - 주 테이블
{% highlight java %}
package com.example.demo.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;

import com.fasterxml.jackson.annotation.JsonIdentityInfo;
import com.fasterxml.jackson.annotation.ObjectIdGenerators;

import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Data
public class Host2 {

	// 1:1 양방향
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String name;

	@OneToOne
	@JoinColumn(name = "target_id")
	@JsonIdentityInfo(generator = ObjectIdGenerators.IntSequenceGenerator.class)
	private Target2 target;

	@Builder
	public Host2(String name, Target2 target) {
		this.name = name;
		this.target = target;
	}

}
{% endhighlight %}

### 2. Target
{% highlight java %}
package com.example.demo.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToOne;

import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Data
public class Target2 {

	// 1:1 양방향
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String name;

	@OneToOne(mappedBy = "target")
	private Host2 host;

	@Builder
	public Target2(String name, Host2 host) {
		this.name = name;
		this.host = host;
	}
}
{% endhighlight %}

## 3. HostRepository
{% highlight java %}
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.domain.Host2;

@Repository
public interface Host2Repository extends JpaRepository<Host2, Long> {
}
{% endhighlight %}

## 4. TargetRepository
{% highlight java %}
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.domain.Target2;

@Repository
public interface Target2Repository extends JpaRepository<Target2, Long> {
}
{% endhighlight %}

## 5. HostTargetService
{% highlight java %}
package com.example.demo.service;

import org.springframework.stereotype.Service;

import com.example.demo.domain.Host2;
import com.example.demo.domain.Target2;
import com.example.demo.repository.Host2Repository;
import com.example.demo.repository.Target2Repository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class HostTargetService2 {

	private final Target2Repository targetRepo;
	private final Host2Repository hostRepo;

	public void save() {
		Target2 target = Target2.builder().name("target2").build();
		Host2 host = Host2.builder().name("host2").target(target).build();

		targetRepo.save(target);
		hostRepo.save(host);
	}

	public Target2 read1() {

		return targetRepo.findById(1L).get();
	}

	public Host2 read2() {
		return hostRepo.findById(1L).get();
	}
}

{% endhighlight %}

## 6. HostTargetController
{% highlight java %}
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.domain.Host1;
import com.example.demo.domain.Host2;
import com.example.demo.domain.Target1;
import com.example.demo.domain.Target2;
import com.example.demo.service.HostTargetService1;
import com.example.demo.service.HostTargetService2;

import io.swagger.annotations.Api;
import lombok.RequiredArgsConstructor;

@Api
@RestController
@RequestMapping("/host-target")
@RequiredArgsConstructor
public class HostTargetController {
	private final HostTargetService1 hostTargetService1;
	private final HostTargetService2 hostTargetService2;

    @PostMapping("/save2")
	public void save2() {
		hostTargetService2.save();
	}

	@GetMapping("/read-host2")
	public Host2 readparent2() {
		return hostTargetService2.read2();
	}

	@GetMapping("/read-target2")
	public Target2 readchild2() {
		return hostTargetService2.read1();
	}
}

{% endhighlight %}

## 7. Swagger & DB 확인하기

### 1. save1 실행
<figure>
	<img src="/assets/img/Host_Target_Swagger1.png">
</figure>

save1 실행하면 test 계정에 host1 과 target1 테이블이 만들어지며, 데이터 insert를 확인할 수 있다.
 
Eclipse 콘솔창 log에 찍힌 Insert query도 확인해보자.
<figure>
	<img src="/assets/img/Insert_Console.png">
</figure>

### 2. DBeaver 확인하기 (Data Insert 와 ERD)
<figure>
	<img src="/assets/img/Host_Target_ERD.png">
</figure>

1) host1 과 target1 테이블 확인
2) ERD 확인 <br> 
target1 테이블의 PK인 id가 host1 테이블의 FK로 설정.

### 3. read-host1 조회
<figure>
	<img src="/assets/img/Host1_OneToOne.png">
</figure>

log에 찍힌 Select query 확인하면 아래와 같다. 
<figure>
	<img src="/assets/img/Host1_Read_Console.png">
</figure>
Join된 query 확인이 가능하다. 
### 4. read-target1 조회
<figure>
	<img src="/assets/img/Target1_OneToOne.png">
</figure>
