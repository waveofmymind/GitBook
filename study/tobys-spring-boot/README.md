---
description: 토비의 스프링 부트
---

# 🌱 Toby's Spring Boot

{% hint style="info" %}
본 항목은 '토비의 스프링 부트'를 수강하면서 제가 개인적으로 요약할 필요가 있는 내용만 선택적으로 기록했습니다.

강의에는 더 많고 좋은 내용이 있기 때문에 직접 수강하시는 것을 권합니다.&#x20;
{% endhint %}

{% embed url="https://www.inflearn.com/course/%ED%86%A0%EB%B9%84-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EC%9D%B4%ED%95%B4%EC%99%80%EC%9B%90%EB%A6%AC/dashboard" %}

## :blue\_book: 스프링 부트

스프링을 쉽게 사용할 수 있게 해준다.

개인적인 관점으로, 애자일 방식의 짧은 기간동안의 빠른 결과물을 내는 스프린트를 진행하는 조직 특성상

스프링 부트가 가져다주는 장점은 무시할 수 없다.

즉, 스프링을 사용해서 빠르게 결과물을 가져다줄 수 있는 도구의 모음집이 스프링 부트이다.&#x20;

{% hint style="warning" %}
Spring != Spring Boot
{% endhint %}

## 🎯 스프링 부트의 목표

빠르게 스프링을 사용하는 경험을 제공한다.

비기능적인 부가 기술을 제공한다.(모니터링, 보안, 설정 등)

코드 설정 및 XML 설정을 하지 않는다.



## Containerless

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

웹 애플리케이션 서버는 웹 서버로부터의 요청을 동적으로 처리하기 위해서 다양한 Web Component가 존재하며,

이는 자바 기준, `Servlet`이 된다.

그리고 이 Servlet이 모인 컨테이너가 `Tomcat`이다.

Servlet Container 뒤쪽에 있는 것이 Spring Container이다.

서블릿을 통해 외부의 요청을 받아 스프링 컨테이너로 넘겨주고, 스프링은 특정 빈에게 요청을 위임한다.

그러나 스프링을 사용하면서 서블릿 컨테이너를 다루기 위해서는 다양한 설정이 필요하다.(ex. WEB/INF, jar, deploy 등)

또한 Tomcat 외에 다른 Servlet Container를 사용할 경우 해당 Sevlet Container에 대한 설정을 알아야한다.

이러한 문제를 해결하기 위해 standalone으로 실행할 수 있는 것이 `Spring Boot`이다.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

단지 메인 메서드를 실행하는 것으로 스프링과 서블릿 컨테이너를 실행할 수 있는 간편함을 제공하는 것.

그것이 Containerless Application이다.

## Opinionated

스프링 부트가 지향하는 것

개발자는 고객에게 서비스를 제공하기 위한 기능을 개발하는 것에 충실하고, 설정은 스프링 부트가 해준다는 의미이다.

스프링 생태계 프로젝트, 표준 자바 기술, 오픈소스 기술 등을 단지 의존성을 추가하는 것 만으로 손쉽게 사용할 수 있다.

버저닝의 경우도 Spring Boot 버전에 맞추어주며, 별도의 설정 없이도 디폴트 설정값을 제공한다.







