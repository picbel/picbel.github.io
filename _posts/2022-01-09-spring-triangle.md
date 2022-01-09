---
sidebar:
nav: "main"
layout: single
title:  "Spring Triangle"
categories: [Spring]
tags: [Spring,IOC,AOP,PSA]
teaser: /assets/images/teaser/spring.png
---

<p align="center"><img src="/assets/images/teaser/spring.png" width="70%" height="auto"></p>

스프링의 핵심 3대 요소인 Spring Triangle의 포스트이다.


Spring Triangle - 스프링의 핵심 3대요소
---
- 제어 역전(IoC, Inversion of Control)
- 관점 지향 프로그래밍(AOP, Aspect Oriented Programming)
- 서비스 추상화(PSA, Portable Service Abstraction)

제어 역전(IoC, Inversion of Control) / DI(의존성 주입)
---
스프링의 가장 기본이 되는 기술이자 스프링의 핵심 개발원칙\
유연한 확장이 가능하게 하기 위해 쓰이는 기술이다.
기존 자바에서 의존성을 자기 자신이 생성하여 맺는다.
```
  class Member {
      //main
  
      Team team = new Team();
  }
```
하지만 스프링에서는 의존성을 이렇게 주입한다.
```
@Service
public class Member {

    private final Team team;

    // 스프링 4.3이상 부터는 생성자방식의 인젝션의 경우 @Autowired 생략 가능하다
    public Member(Team team) {
        this.team = team;
    }
```
즉 의존성을 직접 만드는게 아닌 제3의 외부로 부터 의존성을 주입받는다.
먼저 이렇게 달라진 의존방식으로 얻는 이점이 있다
1. 기존 방식과 달리 Team이 변경 및 확장되어도 Member는 영향을 받지 않게된다. 또한 Member관점 에서 보더라도 변경 없이 재사용이 가능하다.
2. 오브젝트의 생명주기를 제어 할수 있게된다. (오브젝트를 싱글톤으로 사용하고 관리한다. 오브젝트를 설계할때 싱글톤을 고려하지않고 자유롭게 설계하여도 된다.)
3. 테스트시 Mock같은 테스트 대역을 이용하기 쉬워진다.


관점 지향 프로그래밍(AOP, Aspect Oriented Programming)
---
<p align="center"><img src="/assets/images/posts/aop.png" width="60%" height="auto"></p>

어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화한다.\
(여기서 모듈화란 어떤 공통된 로직이나 기능을 하나의 단위로 묶는 것을 말한다)\
핵심적인 관점이란 우리가 적용하고자 하는 핵심 비즈니스로직이 된다. 또한 부가적인 관점은 핵심 로직을 실행하기 위해서 행해지는 트랜젝션, 로깅을 예로 들수있다.\

하지만 한계점이 없는것이 아니다. 프록시방식의 AOP의 경우 프록시의 한계점을 그대로 가지고있다.\
(자가 호출 불가, 접근제한자가 public)


서비스 추상화(PSA, Portable Service Abstraction)
---
추상화 계층을 이용하여 어떤 기술을 내부에 숨기고 개발자에게 편의성을 제공해주는 것을 Service Abstraction이라 한다. \
더하여 Service Abstraction으로 제공되는 기술을 다른 기술 스택으로 간편하게 바꿀 수 있는 확장성을 갖고 있는 것이 Portable Service Abstraction이다.

> Spring의 @Controller 애노테이션이 붙어있는 클래스에서 @GetMapping,@PostMapping 같은 @RequestMapping애노테이션을 사용해서 요청을 맵핑한다. \
실제로는 서블릿 기반으로 코드가 동작하지만 서블릿 기술은 추상화계층에 의해 숨겨진 것이다

