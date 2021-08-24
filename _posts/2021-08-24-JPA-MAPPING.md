---
layout: post
title: "[Spring Boot] JPA Mapping 연관관계"
date: 2021-08-24
excerpt: "JPA Mapping 연관관계"
tags: [SpringBoot, Java, REST API, JPA, Hibernate, Swagger, Eclipse]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg

---



# 연관관계 (단방향과 양방향)

* 연관관계 매핑 
- 방향 : 단방향, 양방향
- 다중성 : 일대일, 일대다, 다대일, 다대다
- 연관관계 주인 : 객체 양방향 연관관계는 주인이 필요함! 

# 1. 일대일 단방향 

1. Host1  
{% highlight java %}
package com.example.demo.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToOne;

import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity				//JPA가 관리함
@NoArgsConstructor	// 기본생성자
@Data				//@ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor을 한번에 사용하는 어노테이션
public class Host1 {

	// 1:1 단방향
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String name;

	@OneToOne
	@JoinColumn(name = "target_id")
	private Target1 target;

	@Builder
	public Host1(String name, Target1 target) {
		this.name = name;
		this.target = target;
	}

} 
{% endhighlight %}

2. Target1 
{% highlight java %}
package com.example.demo.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Data
public class Target1 {

	// 1:1 단방향
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String name;

	@Builder
	public Target1(String name) {
		this.name = name;
	}
}  
{% endhighlight %}

3. Host1Repository
{% highlight java %}
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.domain.Host1;

@Repository
public interface Host1Repository extends JpaRepository<Host1, Long> {

}

{% endhighlight %}

4. TargetRepository
{% highlight java %}
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import com.example.demo.domain.Target1;

@Repository
public interface Target1Repository extends JpaRepository<Target1, Long> {

}

{% endhighlight %}

5. HostTargetService1
{% highlight java %}
package com.example.demo.service;

import org.springframework.stereotype.Service;

import com.example.demo.domain.Host1;
import com.example.demo.domain.Target1;
import com.example.demo.repository.Host1Repository;
import com.example.demo.repository.Target1Repository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class HostTargetService1 {

	private final Target1Repository targetRepo;
	private final Host1Repository hostRepo;

	public void save() {
		Target1 target = Target1.builder().name("target1").build();
		Host1 host = Host1.builder().name("host1").target(target).build();

		targetRepo.save(target);
		hostRepo.save(host);
	}

	public Target1 read1() {

		return targetRepo.findById(1L).get();
	}

	public Host1 read2() {
		return hostRepo.findById(1L).get();
	}
}
{% endhighlight %}

6. HostTargetController
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

	@PostMapping("/save1")
	public void save1() {
		hostTargetService1.save();
	}

	@GetMapping("/read-host1")
	public Host1 readparent1() {
		return hostTargetService1.read2();
	}

	@GetMapping("/read-target1")
	public Target1 readchild1() {
		return hostTargetService1.read1();
	}

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

