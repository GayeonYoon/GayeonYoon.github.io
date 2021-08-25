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

import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Data
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
