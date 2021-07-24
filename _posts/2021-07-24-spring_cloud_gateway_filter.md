---
title:  "스프링 클라우드 게이트웨이"
excerpt: ""

categories:
  - java
  - cloud
  - gateway
last_modified_at: 2020-07-24T08:06:00-05:00
---


# API 게이트웨이란?

- 인증 / 모니터링 / 오케스트레이션 등과 같은 기능을 포함한 향상된 Reverse Proxy. 
- 많이 쓰이는 게이트웨이들은 넷플릭스 `Zuul`,  스프링 진영의 `Spring Cloud Gateway` 등이 있다.


# 스프링 클라우드 게이트웨이란?

- 논블로킹(non-blocking), 비동기(Asynchronous) 방식의  Netty Server를 사용한다. 
- 때문에 서블릿 컨테이너나 WAR로 빌드하면 동작하지 않는다.
- 스프링 클라우드 게이트웨이는 아래 3가지 핵심 요소를 기억해야 한다.
  1. Route: 대상 URI, 조건부 집합(Predicates), 각종 필터들로 이루어진 요청을 라우팅할 대상들이라고 생각하자.
  2. Predicate: Java 8의 Function Predicate로, 라우팅에 필요한 조건이다. 예로 path = /abc 같은 조건이나, request header의 특정 키-값도 조건으로 사용할 수 있다.
  3. Filter: Spring Framework의 WebFilter 인스턴스이다.  Filter에서는 요청 전후로 요청 / 응답을 추가/ 수정 할 수 있다.



# filter 적용 방법1 - config 파일


```java
@Configuration
public class FilterConfig {

    @Bean
    public RouteLocator gatewayRoutes(RouteLocatorBuilder routeLocatorBuilder) {
        return routeLocatorBuilder.routes()
                .route(r -> r.path("/first-service/**")
                        .filters(f -> f.addRequestHeader("f-req", "f-req-val")
                                .addResponseHeader("f-res", "f-res-val"))
                        .uri("http://localhost:8081"))
                .route(r -> r.path("/second-service/**")
                        .filters(f -> f.addRequestHeader("s-req", "s-req-val")
                                .addResponseHeader("s-res", "s-res-val"))
                        .uri("http://localhost:8082"))
                .build();
    }
}


```


# filter 적용 방법2 - yml 파일


```java
spring:
  application:
    name: apigateway-service
  cloud:
    gateway:
      routes:
        - id: first-service
          uri: http://localhost:8081
          predicates:
            - Path=/first-service/**
          filters:
            - AddRequestHeader=f-req, f-req-val2
            - AddResponseHeader=f-res, f-res-val2
        - id: second-service
          uri: http://localhost:8082
          predicates:
            - Path=/second-service/**
          filters:
            - AddRequestHeader=s-req, s-req-val2
            - AddResponseHeader=s-res, s-res-val2


```