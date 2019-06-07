---
layout: post
title:  "2019-05-21-study"
date:   2019-05-21 09:24:10
author: negan k
categories: Infra
tags: aws
---

# 아키텍쳐 

>Netflix 기반 

## 1. API Gateway

### Zuul

**주요 기능**
1. 인증과 보안
2. 모니터링과 분석
3. 동적 라우팅
4. 트래픽 조정

## 2. Load Balancer

### Ribbon

**주요 기능**
1. 클라이언트 측 로드 밸런서
2. 여러 서버를 라운드로빈 방식의 부하 분산 기능을 제공(여러 알고리즘 사용 가능)
3. Spring Cloud Config와 결합하여, 서버 목록을 제공받아 사용할 수 있음

## 3. 동적 서비스 등록, 발견

### Eureka

**주요 기능**
1. 서버 개수 동적 조절 - elastic service에서 URL을 환경설정 파일에 정적으로 미리 지정하는 것은 적합하지 않음
2. IP주소는 예측할 수 없으며, 어떤 파일에서 정적으로 관리하기가 어려움

## 4. Circuit Breaker

### Hystrix

**주요 기능**
1. Netflix에서 Circuit Breaker Pattern을 구현한 라이브러리이다. Micro Service Architecture에서 장애 전파 방지를 할 수 있다.

### 아키텍쳐 참고글
https://www.slideshare.net/ifkakao/msa-api-gateway/

### 구현기술 참고글
https://supawer0728.github.io/2018/03/11/Spring-Cloud-Zuul/