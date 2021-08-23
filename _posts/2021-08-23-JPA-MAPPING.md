---
layout: post
title: "[Spring Boot] JPA 연관관계 매핑"
date: 2021-08-23
excerpt: "JPA 연관관계 매핑"
tags: [Eclipse, SpringBoot, JPA, Mapping]
comments: false
spring: true
feature: /assets/img/spring_boot_logo.jpg
---

# 연관관계 (단방향과 양방향)

* 연관관계 매핑 
- 방향 : 단방향, 양방향
- 다중성 : 일대일, 일대다, 다대일, 다대다
- 연관관계 주인 : 객체 양방향 연관관계는 주인이 필요함! 

* 1. 일대일 단방향 매핑
{% highligh java%}

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



{% endhighlight%}
