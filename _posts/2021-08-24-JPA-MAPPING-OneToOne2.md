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
### 1. save2 실행 후

### 2. read-host2 조회
<figure>
	<img src="/assets/img/Host2_OneToOne.png">
</figure>
  
### 4. read-target2 조회
<figure>
	<img src="/assets/img/Target2_OneToOne.png">
</figure>


<hr>
## @JsonIdentityInfo 
Host2에서 	@JsonIdentityInfo를 지우면 어떤일이 일어날까 ? 
{% highlight java %}
	@OneToOne
	@JoinColumn(name = "target_id")
//	@JsonIdentityInfo(generator = ObjectIdGenerators.IntSequenceGenerator.class)
	private Target2 target;
{% endhighlight %}

아래와같이 무한 순환참조가 일어난다. 
<figure>
	<img src="/assets/img/Not_JsonIdentityInfo.png">
</figure>

JPA ORM으로 개발하다보면 꽤나 많은 양방향 참조가 필요한 모델들이 있습니다. 그런데 문제는 이런 양방향관계를 가진 객체를 직렬화 하려고 할 때 무한참조가 되어 StackOverFlow를 발생시키는데요.
그렇다고 해서 ORM이 가진 장점을 포기하고 단방향으로만 구성 할 수는 없을겁니다.

때문에 @JsonIdentityInfo을 통해 순환참조될 대상의 식별키로 구분해 더이상 순환참조되지 않게 하는 방법이 있다. 


보시는 것처럼 Property를 설정할 수 있는 PropertyGenrator ,

숫자형 ID로 설정하는 IntSequenceGenerator, 문자열 ID로 설정하는 StringIdGenerator UUID 형태의 ID로 설정하는 UUIDGenerator 등이 있습니다.

저는 현재 숫자형 ID로 모든 엔티티가 이루어져 있어, IntSequenceGenerator 사용하고,

property 명을 따로 명시하지 않으면 기본값 직렬화 시 객체의 id가 @id 로 설정되기 때문에 저는 property명을 명시해주었습니다.



변경 후 테스트 결과는?
정상적으로 한번 직렬화 된 객체는 id값으로만 직렬화 되어지고 있습니다!
